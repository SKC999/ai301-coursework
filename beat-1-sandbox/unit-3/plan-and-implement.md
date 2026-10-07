# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

SKC999

**Plan comment**

Link: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/1#issuecomment-6030137863

Text as posted:

````markdown
## Plan for #1

My repro above showed the skip check never works. Both ingests logged `Could not check if source already ingested` and the second one ran with `skipped=False` and `embed_calls=2`.

The cause is in `ingestion/pipeline.py`. `_check_skip` passes the string `"IngestedSource"` to `db_session.query()` and the error is swallowed. `_record_ingested_source` never saves a row either. I read `core/models/ingested_source.py` and the model has no `source_id` column. It has `profile_id` and `source_type` and `content_hash`. So the one-line fix suggested above would still not find a match.

My plan is one bounded change in `ingestion/pipeline.py` on the branch `fix/1-ingest-skip-check` in my fork.

1. `_check_skip` runs `select(IngestedSource.id)` on `profile_id` and `source_type` and `content_hash`. The hash is the full SHA256 of the existing source id.
2. `_record_ingested_source` adds and commits an `IngestedSource` row with that hash.
3. The three ingest methods pass `profile_id` to the check.

I will add `tests/unit/test_pipeline_dedup.py` and re-run my repro script on its in-memory SQLite engine. The fix is working only if the second ingest returns `skipped=True` with `embed_calls=1` and no warning. I will post what the run shows.

I am leaving out cleanup of old vectors when a repo is re-ingested with changed content. That is a separate change. I have only tested on SQLite so far and have not confirmed the insert against Postgres.

I used an AI assistant to help draft this plan and comment. I will review every diff and run every test myself.
````

---

## Your branch

**Branch**

`fix/1-ingest-skip-check`

It is on my fork https://github.com/SKC999/pathreview-ai301-fa26-s3 and the fix is in commits `d94cdda` (the change) and `f3c69cc` (the regression tests).

**Evidence**

I re-ran my unit 2 repro script `repro_dup.py` with the one setup change from my test plan. The script now creates the `ingested_sources` table on its in-memory SQLite engine because the fixed code reads a real table. I also ran the new regression test file. Both runs used the same Linux sandbox with `TIKTOKEN_CACHE_DIR` set as described under Deviations in plan.md.

The before run is on commit `2f4e82f` before the fix.

```
$ python repro_dup.py
OS: Linux-6.8.0-138-generic-x86_64-with-glibc2.35
Python: 3.10.12
chromadb: 1.5.9  sqlalchemy: 2.0.54

2026-10-07 03:08:34 [info     ] Starting README ingestion      profile_id=profile-1 repo_name=demo-repo source_id=readme_profile-1_demo-repo_df474c7dc8df5311
2026-10-07 03:08:34 [warning  ] Could not check if source already ingested error="Textual column expression 'IngestedSource' should be explicitly declared with text('IngestedSource'), or use column('IngestedSource') for more specificity" source_id=readme_profile-1_demo-repo_df474c7dc8df5311
2026-10-07 03:08:34 [info     ] README parsed successfully     heading_count=3 word_count=22
2026-10-07 03:08:34 [info     ] README chunked successfully    chunk_count=3
2026-10-07 03:08:34 [info     ] Starting batch embedding processing chunk_count=3
2026-10-07 03:08:34 [info     ] Processing embedding batch     batch_end=3 batch_num=1 batch_start=0 total=3
2026-10-07 03:08:34 [info     ] Generated embeddings for batch embedding_count=3
2026-10-07 03:08:34 [debug    ] Stored embedding in vector DB  embedding_id=readme_profile-1_demo-repo_df474c7dc8df5311_chunk_0
2026-10-07 03:08:34 [debug    ] Stored embedding in vector DB  embedding_id=readme_profile-1_demo-repo_df474c7dc8df5311_chunk_1
2026-10-07 03:08:34 [debug    ] Stored embedding in vector DB  embedding_id=readme_profile-1_demo-repo_df474c7dc8df5311_chunk_2
2026-10-07 03:08:34 [info     ] Batch embedding processing complete stored_count=3
2026-10-07 03:08:34 [info     ] README embeddings stored       chunk_count=3
2026-10-07 03:08:34 [info     ] Recording ingested source      chunk_count=3 profile_id=profile-1 source_id=readme_profile-1_demo-repo_df474c7dc8df5311 source_type=readme
ingest #1: skipped=False chunk_count=3 embed_calls=1 vectors_in_store=3
2026-10-07 03:08:34 [info     ] Starting README ingestion      profile_id=profile-1 repo_name=demo-repo source_id=readme_profile-1_demo-repo_df474c7dc8df5311
2026-10-07 03:08:34 [warning  ] Could not check if source already ingested error="Textual column expression 'IngestedSource' should be explicitly declared with text('IngestedSource'), or use column('IngestedSource') for more specificity" source_id=readme_profile-1_demo-repo_df474c7dc8df5311
2026-10-07 03:08:34 [info     ] README parsed successfully     heading_count=3 word_count=22
2026-10-07 03:08:34 [info     ] README chunked successfully    chunk_count=3
2026-10-07 03:08:34 [info     ] Starting batch embedding processing chunk_count=3
2026-10-07 03:08:34 [info     ] Processing embedding batch     batch_end=3 batch_num=1 batch_start=0 total=3
2026-10-07 03:08:34 [info     ] Generated embeddings for batch embedding_count=3
2026-10-07 03:08:34 [debug    ] Stored embedding in vector DB  embedding_id=readme_profile-1_demo-repo_df474c7dc8df5311_chunk_0
2026-10-07 03:08:34 [debug    ] Stored embedding in vector DB  embedding_id=readme_profile-1_demo-repo_df474c7dc8df5311_chunk_1
2026-10-07 03:08:34 [debug    ] Stored embedding in vector DB  embedding_id=readme_profile-1_demo-repo_df474c7dc8df5311_chunk_2
2026-10-07 03:08:34 [info     ] Batch embedding processing complete stored_count=3
2026-10-07 03:08:34 [info     ] README embeddings stored       chunk_count=3
2026-10-07 03:08:34 [info     ] Recording ingested source      chunk_count=3 profile_id=profile-1 source_id=readme_profile-1_demo-repo_df474c7dc8df5311 source_type=readme
ingest #2: skipped=False chunk_count=3 embed_calls=2 vectors_in_store=3

documents in store: 3  unique: 3

$ python -m pytest tests/unit/test_pipeline_dedup.py -q
        assert first.skipped is False
>       assert second.skipped is True
E       AssertionError: assert False is True
E        +  where False = IngestResult(source_id='readme_8f2c1d4e-0b6a-4c3e-9a51-2d7f0e6b9c10_demo-repo_df474c7dc8df5311', chunk_count=3, skipped=False, skip_reason=None).skipped
        assert changed.skipped is False
        assert embedder.calls == 2
>       assert _records(session) == 2
E       assert 0 == 2
E        +  where 0 = _records(<sqlalchemy.orm.session.Session object at 0x719af849fd90>)
2 failed, 1 passed, 1 warning in 1.35s
```

The after run is on the pushed branch at commit `f3c69cc` with the fix.

```
$ python repro_dup.py
OS: Linux-6.8.0-138-generic-x86_64-with-glibc2.35
Python: 3.10.12
chromadb: 1.5.9  sqlalchemy: 2.0.54

2026-10-07 03:21:21 [info     ] Starting README ingestion      profile_id=profile-1 repo_name=demo-repo source_id=readme_profile-1_demo-repo_df474c7dc8df5311
2026-10-07 03:21:21 [info     ] README parsed successfully     heading_count=3 word_count=22
2026-10-07 03:21:21 [info     ] README chunked successfully    chunk_count=3
2026-10-07 03:21:21 [info     ] Starting batch embedding processing chunk_count=3
2026-10-07 03:21:21 [info     ] Processing embedding batch     batch_end=3 batch_num=1 batch_start=0 total=3
2026-10-07 03:21:21 [info     ] Generated embeddings for batch embedding_count=3
2026-10-07 03:21:21 [debug    ] Stored embedding in vector DB  embedding_id=readme_profile-1_demo-repo_df474c7dc8df5311_chunk_0
2026-10-07 03:21:21 [debug    ] Stored embedding in vector DB  embedding_id=readme_profile-1_demo-repo_df474c7dc8df5311_chunk_1
2026-10-07 03:21:21 [debug    ] Stored embedding in vector DB  embedding_id=readme_profile-1_demo-repo_df474c7dc8df5311_chunk_2
2026-10-07 03:21:21 [info     ] Batch embedding processing complete stored_count=3
2026-10-07 03:21:21 [info     ] README embeddings stored       chunk_count=3
2026-10-07 03:21:21 [info     ] Recorded ingested source       chunk_count=3 profile_id=profile-1 source_id=readme_profile-1_demo-repo_df474c7dc8df5311 source_type=readme
ingest #1: skipped=False chunk_count=3 embed_calls=1 vectors_in_store=3
2026-10-07 03:21:21 [info     ] Starting README ingestion      profile_id=profile-1 repo_name=demo-repo source_id=readme_profile-1_demo-repo_df474c7dc8df5311
2026-10-07 03:21:21 [info     ] Source already ingested, skipping source_id=readme_profile-1_demo-repo_df474c7dc8df5311
ingest #2: skipped=True chunk_count=0 embed_calls=1 vectors_in_store=3

documents in store: 3  unique: 3

$ python -m pytest tests/unit/test_pipeline_dedup.py -q
3 passed, 1 warning in 1.35s
```

The second ingest went from `skipped=False` with `embed_calls=2` to `skipped=True` with `embed_calls=1`. The `Could not check if source already ingested` warning is gone. The regression tests went from 2 failed to 3 passed.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run 1 scored 19/20 with every category matched (`clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`). This is the run saved in `eval-run.txt`.

It cleared the 18/20 bar and the category floor on the first full run. So I made no revisions and did no partial `--only` runs after it. The final score is 19/20.

**Package analysis**

I picked `pkg-14` (zellij-org/zellij#5174). The gold label is accept and my rubric decided reject. It failed only `grounded-cause`.

The plan blames the reattach path for wiring stdin to the pane before the OSC color query responses are consumed. The repro has two control runs. Version 0.44.1 never leaks. After `rm -rf ~/.cache/zellij` the next attach is clean and the one after leaks again. My grader read the cache control as pointing the failure at cache state. It wrote that the plan "defers caching and only hand-waves it". My pass condition fails a plan when "the evidence pins the failure somewhere the plan does not look". So the grader treated the cache control as that kind of evidence.

I think the gold label is right. The plan does explain the cache control. An empty cache sends the colors down the fresh-attach path once and the fresh attach is clean. The cache control does not show the reattach handshake working. It only shows one attach taking a different path. My check cannot tell a control that rules a cause out from a control the plan explains. I left it alone because the bar was met and loosening it risks the wrong-cause packages.

**Check rationale**

This is the check as it reads in my rubric.md.

| grounded-cause | The plan's stated cause read against every step, control run and artifact in the repro evidence (and any maintainer finding in the thread highlights) | Pass if the stated cause explains the actual behavior the repro shows and no step or control in the repro evidence rules that cause out. Fail if a control run shows the blamed component working, if the evidence pins the failure somewhere the plan does not look, or if the plan names no cause at all. | required |

It reads this way because the wrong-cause packages all share one trap. The plan sounds confident and often matches the issue's own guess but a control run in the repro shows the blamed part working. That is how `pkg-01` and `pkg-07` and `pkg-11` are built. So the pass condition does not ask whether the cause sounds right. It asks whether any step or control rules it out. My procedure backs this up by reading the repro evidence before the plan. That way the grader judges the cause against the evidence and is not anchored by how sure the plan sounds. I rejected a looser wording that only asked if the cause "matches the repro". A cause can match the symptom and still be contradicted by a control.

**Trade-offs**

The check gives up `pkg-14`. It is strict about controls so it can reject an honest plan whose control looks like it points elsewhere even when the plan explains that control. That is the one miss in my run. I accept that it will miss cases like this. Loosening it to "unless the plan explains the control" would let a confident wrong plan pass by writing a story about its control. All four wrong-cause packages agreed with gold and I did not want to trade any of them for one clear accept. I did not change the check so no other package could have flipped.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
