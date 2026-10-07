# Plan for #1: the already-ingested check never skips a repeat ingest

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/1

Branch: `fix/1-ingest-skip-check` on my fork SKC999/pathreview-ai301-fa26-s3

## Diagnosis

My unit 2 repro ingested the same README twice through the real `IngestionPipeline`. Both runs logged this warning.

```
[warning  ] Could not check if source already ingested error="Textual column expression 'IngestedSource' should be explicitly declared with text('IngestedSource'), or use column('IngestedSource') for more specificity" source_id=readme_profile-1_demo-repo_df474c7dc8df5311
```

The result lines showed the second ingest ran the whole pipeline again.

```
ingest #1: skipped=False chunk_count=3 embed_calls=1 vectors_in_store=3
ingest #2: skipped=False chunk_count=3 embed_calls=2 vectors_in_store=3
```

The cause has two parts in `ingestion/pipeline.py`. `_check_skip` passes the string `"IngestedSource"` to `db_session.query()` so SQLAlchemy raises before any query runs. The broad `except` turns that into the warning above and returns `None`. `_record_ingested_source` only writes a log line and never saves a row. So even a working query would find nothing.

The one-line fix suggested in the thread swaps the string for the class and keeps `.filter_by(source_id=source_id)`. That would still fail because the `IngestedSource` model in `core/models/ingested_source.py` has no `source_id` column. It has `profile_id` and `source_type` and `content_hash` instead.

My repro also showed the vector store held 3 vectors after both runs. So for identical content the visible harm is a second embedding call and a full re-run. It is not a doubled vector count.

## Scope

In scope is making the skip check work for all three ingest methods. That means a real query in `_check_skip` and a real insert in `_record_ingested_source`. I will also add a regression test.

Out of scope is removing old vectors when a repo is re-ingested with changed content. That needs a delete step in the vector store and is a separate change. Also out of scope are the duplicate index names in the model and any change to how the API calls the pipeline. No route calls `IngestionPipeline` today.

## Files

- `ingestion/pipeline.py` for `_check_skip` and `_record_ingested_source` and the three call sites
- `tests/unit/test_pipeline_dedup.py` as a new regression test

## Approach

1. Import `IngestedSource` from `core.models` and `select` from SQLAlchemy in `ingestion/pipeline.py`.
2. Add a `_dedupe_key(source_id)` helper that returns the full SHA256 of the source id. The source id already holds the profile and the source name and a short content hash. Its full hash is 64 characters and fits the model's `content_hash` column.
3. Change `_check_skip` to take `profile_id` and run `select(IngestedSource.id)` filtered on `profile_id` and `source_type` and `content_hash == _dedupe_key(source_id)`. Return the skip result when a row exists. Keep the existing warning path for real database errors.
4. Pass `profile_id` from the three call sites in `ingest_resume` and `ingest_readme` and `ingest_repo_metadata`.
5. Change `_record_ingested_source` to add an `IngestedSource` row with the same key and `chunk_count` and then commit. Roll back and log an error if the insert fails.
6. Add the regression test described below.

## Test plan

1. Re-run my unit 2 repro script with one setup change. The fixed code reads a real table so the script creates the `ingested_sources` table on its in-memory SQLite engine before ingesting. I expect `ingest #2: skipped=True chunk_count=0 embed_calls=1 vectors_in_store=3` and no warning line.
2. Run the same script on `main` first as the before case. I expect the same output I posted in unit 2 with `skipped=False` and `embed_calls=2` on the second ingest.
3. Run `pytest tests/unit/test_pipeline_dedup.py`. I expect 3 passed. The tests check that a second identical ingest is skipped with one embed call and one row. They also check that changed content and a different profile are still ingested.

## Risks and unknowns

- I have not run this against Postgres. My repro used SQLite. The query uses plain column filters so I expect Postgres to behave the same but I have not confirmed it.
- The app's database code is async while the pipeline uses a sync session. No route calls the pipeline yet so I cannot test that wiring. I am leaving it alone.
- The model declares each index twice under the same name. SQLite rejects that on `create_all` so the test creates the table without indexes. I am not changing the model in this fix.
- `profile_id` is a foreign key to `profiles`. In Postgres the insert needs a real profile row. The app creates profiles before ingesting so I expect this to hold but I have not tested it.

## Deviations

The code change matches the plan. I touched only `ingestion/pipeline.py` and the new `tests/unit/test_pipeline_dedup.py`. The approach steps 1 to 6 went in as written on the branch `fix/1-ingest-skip-check`. The change is commit `d94cdda` and the regression tests are commit `f3c69cc`.

The test environment changed. I ran the before and after checks in a Linux sandbox linked to my machine. It had Python 3.10.12 and SQLAlchemy 2.0.54 instead of the Windows 11 setup with Python 3.13.3 and SQLAlchemy 2.1.1 from my repro. The chromadb version was the same at 1.5.9.

That sandbox cannot download the tiktoken `cl100k_base` file that the semantic chunker loads. I pointed `TIKTOKEN_CACHE_DIR` at a local copy of that file and checked its SHA256 against the hash tiktoken expects. No code changed because of this.

The repro script setup change needed one more import than I wrote. Creating the table needs `IngestedSource` from `core.models` and `CreateTable` from SQLAlchemy. The `"profile-1"` profile id from my repro still worked on SQLite so I kept it.

The results matched the test plan. Before the fix the second ingest showed `skipped=False` with `embed_calls=2` and 2 of the 3 new tests failed. After the fix the second ingest showed `skipped=True chunk_count=0 embed_calls=1 vectors_in_store=3` with no warning and all 3 tests passed. My posted plan comment is still accurate so I do not need a follow-up comment about a change of plan.
