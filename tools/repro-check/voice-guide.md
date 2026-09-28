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

## Things I never post

- A guaranteed fix or a fix deadline
- A root cause stated as fact before I have evidence
- "+1" or "same here" with nothing attached
- Output I did not run myself
- A comment I would not stand behind if a maintainer asked me to explain it
