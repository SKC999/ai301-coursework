# Voice guide: how I talk upstream

## Who I am in threads

I am Sai, a recent CS and Data Science graduate making early open source contributions through a course. I work mostly in Python. When I comment, readers can expect plain reports of what I ran and what I saw, and nothing I have not checked.

## Rules I write by

### Rule: Promise only what I control

I say what I will do next, never what the outcome will be.

- Wrong: "I'll have this fixed by Friday."
- Right: "I'm going to reproduce this on the current main branch and post what I find here."

### Rule: Name the actual bug

Every comment names the specific behavior from this issue so it could not be pasted anywhere else.

- Wrong: "Hi, I'd like to work on this issue, please assign me."
- Right: "I'd like to look into the duplicate embeddings that appear when the same file is ingested twice."

### Rule: Show before I claim

I only say "reproduced" or "confirmed" when the output I paste shows it.

- Wrong: "Confirmed, this is definitely a race condition."
- Right: "Running ingestion twice on the same file gives 2 embeddings per chunk (output below). I have not found the cause yet."

### Rule: Say what differs

If my setup differs from the reporter's, I say so up front.

- Wrong: "Reproduced on my machine."
- Right: "Reproduced on Windows 11 with Python 3.12. The issue was reported on macOS, so that part is untested."

### Rule: Disclose AI help

When I used AI tools to draft or check a comment, I say so in one plain line.

- Wrong: (no mention of AI help)
- Right: "I used an AI assistant to help draft this comment and checked every command and output myself."

### Rule: State an approach as a plan, not a promise

A plan comment says what I intend to change and what I have not checked yet. It never says the fix will work before I have run it.

- Wrong: "This will fix the duplicates for good."
- Right: "I plan to make the skip check query the real table. I have not yet tested this against Postgres."

### Rule: A PR title names the behavior it fixes

The title says what changes for a user or caller so a maintainer knows in one line what to review.

- Wrong: "Fix bug"
- Right: "fix(ingestion): skip re-ingesting a source that is already recorded"

### Rule: The description promises exactly what the diff contains

Every change I list is in the diff and every change in the diff is listed. I never write "exactly as planned" unless I checked the diff against the plan.

- Wrong: "Implements the plan in full and cleans up the pipeline."
- Right: "Changes `_check_skip` and `_record_ingested_source` in `ingestion/pipeline.py` and adds one test file. Nothing else changed."

### Rule: Disclose a shortfall as a fact

When I leave something out on purpose or a check fails, I say what and why in one plain sentence. I do not apologize and I do not hide it.

- Wrong: "Sorry, I couldn't get everything working."
- Right: "Cleanup of old vectors on changed content is left out on purpose. It needs a delete step in the vector store and is a separate change."

## Things I never post

- A guaranteed fix or a fix deadline
- A root cause stated as fact before I have evidence
- "+1" or "same here" with nothing attached
- Output I did not run myself
- A comment I would not stand behind if a maintainer asked me to explain it
- A ticked checkbox or "tests pass" that my pasted output does not show
