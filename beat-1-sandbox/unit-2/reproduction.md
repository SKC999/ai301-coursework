# Unit 2 Â· Reproduction

GitHub username: SKC999

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/1

## Claim comment

Link: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/1#issuecomment-5862536862

Text as posted:

> Hi, I'd like to take this issue. I plan to reproduce the duplicate embeddings bug by ingesting the same GitHub repo twice through `ingestion/pipeline.py` and then checking whether the vector store holds two copies of each chunk. I will set up the project from the repo's README on my machine and record my OS and Python version along with the exact commands I run. I will post a repro report here with the output either way. If I cannot reproduce it I will say so and explain what I tried. I used an AI assistant to help draft this comment and I will check every command and output myself.

Reflection. I wrote the claim before reproducing anything. So every sentence promises an action I control and none of it asserts a cause or a result. My skill graded it in live mode and returned accept on claim-specific and ai-policy-followed. It reported the repro checks as not yet applicable.

## Repro comment

Link: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/1#issuecomment-5862549271

Text as posted:

````markdown
## Repro report for #1

I reproduced part of this issue. The already-ingested check never works, so re-ingesting the same source runs the full pipeline again and calls the embedding provider a second time. I did not see duplicate vectors in ChromaDB when the content was identical. Details are below.

### Environment

I ran this on Windows 11 (10.0.26200) with Python 3.13.3. I used my fork of this repo at the current `main` and installed it with `pip install -e .` inside a fresh virtual environment. That installed chromadb 1.5.9 and SQLAlchemy 2.1.1 along with structlog 26.1.0.

I did not start the Docker services or set an API key. The script calls the real `IngestionPipeline` with a real in-memory ChromaDB collection and a real SQLAlchemy session on in-memory SQLite. Only the embedding provider is a stand-in that returns fixed vectors and counts its calls. The app itself uses Postgres. The failure below is raised by SQLAlchemy before any query reaches the database, so I expect Postgres to behave the same way. I have not confirmed that yet.

### Steps

From the repo root run these commands.

```
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -e .
python repro_dup.py
```

This is `repro_dup.py` in full.

```python
import platform
import sys

import chromadb
import sqlalchemy
from sqlalchemy import create_engine
from sqlalchemy.orm import Session

from ingestion.pipeline import IngestionPipeline


class CountingEmbedder:
    def __init__(self):
        self.calls = 0

    def embed(self, texts):
        self.calls += 1
        return [[float(len(t) % 7), 1.0, 0.5] for t in texts]


print(f"OS: {platform.platform()}")
print(f"Python: {sys.version.split()[0]}")
print(f"chromadb: {chromadb.__version__}  sqlalchemy: {sqlalchemy.__version__}")

collection = chromadb.EphemeralClient().get_or_create_collection("repro_issue_1")
session = Session(create_engine("sqlite://"))
embedder = CountingEmbedder()
pipeline = IngestionPipeline(
    vector_db=collection, db_session=session, embedding_provider=embedder
)

readme = (
    "# demo-repo\n\nA small demo project for testing ingestion.\n\n"
    "## Install\n\nRun pip install demo.\n\n"
    "## Usage\n\nImport demo and call run().\n"
)

for run in (1, 2):
    result = pipeline.ingest_readme("profile-1", "demo-repo", readme)
    print(
        f"ingest #{run}: skipped={result.skipped} "
        f"chunk_count={result.chunk_count} "
        f"embed_calls={embedder.calls} "
        f"vectors_in_store={collection.count()}"
    )

docs = collection.get()["documents"]
print(f"documents in store: {len(docs)}  unique: {len(set(docs))}")
```

### Output

This warning is logged on both runs.

```
[warning  ] Could not check if source already ingested error="Textual column expression 'IngestedSource' should be explicitly declared with text('IngestedSource'), or use column('IngestedSource') for more specificity" source_id=readme_profile-1_demo-repo_df474c7dc8df5311
```

These are the result lines.

```
ingest #1: skipped=False chunk_count=3 embed_calls=1 vectors_in_store=3
ingest #2: skipped=False chunk_count=3 embed_calls=2 vectors_in_store=3

documents in store: 3  unique: 3
```

### What happened

I expected the second ingest of identical content to return `skipped=True` without calling the embedding provider again. It returned `skipped=False` and ran the whole pipeline a second time with `embed_calls=2`. The warning shows why. `_check_skip` passes the string `"IngestedSource"` to `db_session.query()`. SQLAlchemy rejects that and the `except` block turns the error into a warning. `_record_ingested_source` also only writes a log line and never saves a record. So the skip check can never find a prior ingest.

The vector store did not double in my run. It held 3 vectors after both ingests. Each vector ID is built from a hash of the content plus the chunk index. ChromaDB 1.5.9 did not add a second copy under an ID it already had. So I could not reproduce identical duplicate chunks with inflated scores for identical content. Duplicates may still appear if the real app writes vectors some other way or uses a different ChromaDB version. They may also appear if a repo is re-ingested with changed content. That would leave the old vectors behind under the old hash. I have not tested those cases yet.

My next step is to check how the app's upload route calls the pipeline and to rerun this with changed content. I will post what I find here.

I used an AI assistant to help write the script and this report. I ran every command myself and the output above is copied from my terminal.
````

Reflection. The result was only a partial reproduction. The broken skip check showed up exactly as the issue describes. The duplicate vectors did not show up because ChromaDB kept the repeated IDs from being stored twice. I reported that split plainly instead of rounding it up to reproduced. My skill graded the full package in live mode and returned accept. Its one miss was the preferred control-run check. I had not yet run a comparison with changed content.

## Run history

My first attempt stopped before grading anything. The harness refused the empty rubric template, which is how it is meant to work. My second attempt crashed on Windows with a FileNotFoundError. The harness launches `claude` directly and my npm install only provided `claude.cmd`. I installed the native Windows build of Claude Code so `claude.exe` was on my PATH. My third attempt was the first full graded run. It agreed with the gold labels on 20 of 20 scored packages and matched every category. I made no rubric changes after it. My fourth attempt was the confirming full run with `--save-run eval-run.txt`. It also agreed on 20 of 20 and is the run committed here.

## Package analysis

Package pkg-20 is the ghostty package. The gold label is reject and my rubric also graded it reject. The reproduction is strong on every proof check. It has a recorded environment and followable steps along with an artifact that matches the issue. The repo facts block states that ghostty requires contributors to disclose all AI usage. Neither comment in the package discloses any. My ai-policy-followed check treats every course package as AI-assisted work. So a repo that requires disclosure fails the package when the disclosure is missing. That single check is why the package fails. Without it this package would have been accepted and the disclosure category would have gone unmatched. The same check passes pkg-03 because ripgrep only bans unreviewed bot comments and that package's comments are specific. It also passes pkg-05 because conda states no disclosure requirement.

## Check rationale

This is the ai-policy-followed pass condition as it reads in my rubric.md.

> If the repo's stated policy requires disclosure of AI use, pass only if the claim comment or repro report explicitly discloses AI assistance; fail if neither does, no matter how good the reproduction is. If the policy only bars unreviewed or bot-generated comments, pass if the comments are specific and read as written by a person who did the work. If the repo states no disclosure requirement, pass.

I split it into three cases because AI policies are not one rule. A single rule of always disclosing would be safe for my own comments. It would wrongly fail eval packages from repos that never ask for disclosure. A single rule of checking for bot-like text would miss ghostty completely. The three cases read the repo's own stated policy first and only then judge the comments against it.

## Trade-offs

My verdict rule counts unclear as fail. That makes the rubric strict. A good package that leaves one piece of evidence ambiguous gets held. I accepted that because posting unverifiable proof costs a maintainer more than holding a package costs me. I also made control-run preferred rather than required. Several gold accepts have no control run, and requiring one would reject honest terse reports. One more trade-off is worth stating. I drafted the rubric with an AI assistant that worked from the gold label notes. That helped every category match on the first graded run. It also means 20 of 20 may overstate how well the rubric would do on packages it has never seen.
