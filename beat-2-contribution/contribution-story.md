# Contribution: The edit-time decision notice ends with a double period when the rationale ends with one

Path: `beat-2-contribution/contribution-story.md`

**Contribution Number:** [1]
**Student:** Sai Karthikeya Cherukuru
**GitHub Username:** SKC999
**Issue:** https://github.com/repowise-dev/repowise/issues/5361
**Pull Request:** https://github.com/repowise-dev/repowise/pull/5381

---

## Toolkit

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
update, or "nothing"]
```

**Unit 5: Scout installed and strategy written with one real grade run**

I installed the scout on October 20 and wrote my fit profile and a search strategy with four gh queries and eight ranking filters. I ran a scan earlier but I never saved its output and it was lost when my course API account stopped working. So I cannot show its verdicts. After I had claimed my issue and opened my PR I graded it with the scout and it returned reject. The only failing check was Issue is unclaimed because my own claim comment and my own PR counted as a claim. The grade also showed that the fit profile in my scope.md was still a placeholder.

**Unit 6: Voice guard and repo memory file not built**

I did not install the voice guard or write a repo memory file for this contribution. I wrote the claim comment with Claude and posted it from my browser. Nothing checked it automatically. I checked it by hand against the repo's claim rules in CONTRIBUTING.md and by reading the whole issue thread first. Nothing broke but I have no hook evidence to show for it. I also never applied the unit 6 scope patch so my rubric is still the version from September 20.

**Unit 7: Ship-gate and manifest not built and checks run by hand**

I did not build the ship-gate or a manifest. I ran the touched test file and ruff on the changed files by hand and confirmed that my new tests fail without the fix. I did not run the full CI commands locally and I said so in the PR description. One near miss happened when I overwrote the repo's PR template. I caught it before submitting and rewrote the description to answer every template item.

**Unit 8: PR monitor not installed and the PR is awaiting review**

I did not install the PR monitor or write a playbook. The PR was opened on November 8 and had no maintainer feedback when I wrote this entry. The repo's CI status was still pending at that time. There was nothing to answer so no draft was sent or discarded. I will check the PR by hand and answer any review in my own words.

---

## Why I Chose This Issue

I searched GitHub for open issues labelled good first issue and bug with Python as the language and no assignee. I added zero comments and no linked pull request to the search. I did not use the course Issue Selection site for this pick. Three earlier candidates were already taken. Opencollab-mcp issues 45 and 46 each had a comment from another contributor asking to work on them. A CSV import issue in a billing app already had three competing pull requests. Issue 5361 in repowise had no comments and no linked PR and nobody had claimed it.

It fits my profile because it is a real Python bug in a project I can run locally and it is not documentation only. The fix is one function and the tests sit right beside it. The issue also says exactly what done looks like and what must not change. I hope to learn how to find my way around a large unfamiliar repo and how its test helpers build a database for a unit test. I also want to practice following a repo's own claim and PR rules.

I installed my scout on October 20 but I did not use it to pick issue 5361. A scan I ran earlier was lost because I never saved the output and my course API account stopped working. I graded the issue with the scout only after I had claimed it and opened my PR. The verdict was reject. Five checks passed. They were that the issue is open and not stale and that the maintainer is active and the repo is not dormant and the issue is scoped. The one failing check was Issue is unclaimed because the only claim comment and the only linked PR are mine.

I am choosing this issue against the scout's verdict and my reason is that the failing check measures my own claim and not someone else's. Nobody else is working on it. That is where the scout fell short on a real search because its unclaimed check cannot tell my own claim from a stranger's. The scout also showed that my scope.md fit profile was still a placeholder so scans had nothing to rank against. I have not yet changed the rubric but I would make that check ignore comments and PRs from my own account. I claimed the issue before starting and my claim comment is at https://github.com/repowise-dev/repowise/issues/5361#issuecomment-6727457957

**Toolkit**

- Tool: issue-scout. I used my written search strategy as a manual checklist to find the issue and then graded issue 5361 with the scout on November 8 after claiming it.
- What it produced: A reject verdict with five passes and one fail. The fail was Issue is unclaimed because my own comment and my own PR 5381 count as a claim.
- Difficulties: The unclaimed check cannot tell my own claim from a stranger's. My scope.md fit profile was still a placeholder. My comment count filter also missed claim comments when I searched by hand.
- What changed: Nothing in the rubric yet. I would make the unclaimed check ignore my own account and I would paste my fit profile into scope.md.

---

## Understanding the Issue

**Problem Description**

When an agent edits a file that a recorded decision governs, repowise prints a one line notice. That notice ends in a double period or in an ellipsis followed by a period when the stored rationale already ends a sentence or was clipped.

**Expected Behavior**

The notice should end with exactly one terminal mark. A rationale that ends in a period should not get a second one. A clipped rationale that ends in an ellipsis should not get a period after it.

**Current Behavior**

The code always adds a period after the rationale and after any sessions suffix. A rationale such as "to ensure usability on narrow terminals." produces a notice ending in two periods. A long rationale that was clipped ends in an ellipsis followed by a period.

**Affected Components**

The function `_edit_decision_notice` in `packages/cli/src/repowise/cli/commands/augment_cmd/decision_inject.py` and its tests in `tests/unit/cli/test_augment_decision_inject.py`.

**Toolkit**

- Tool: Claude on my Pro plan read the issue and the code with me. The course reading tools were not used.
- What it produced: A plain restatement of the bug and the exact lines involved.
- Difficulties: none met
- What changed: nothing

---

## Reproduction Process

**Environment Setup**

I used a Linux sandbox (kernel 6.18) with Python 3.13.18 and uv 0.11.32. I cloned repowise-dev/repowise at commit 89c6369 whose packages report version 0.1.2. I ran `uv sync --all-packages` to install everything. My first sync and test run timed out in the sandbox and left an incomplete environment where pytest was missing. I ran the sync again in the background and it finished. After that the test file ran in about 19 seconds.

**Steps to Reproduce**

1. Clone the repo and run `uv sync --all-packages`.
2. Add two tests that build a decision with the existing `_build_wiki_db` helper. One has the rationale "to ensure usability on narrow terminals." and the other has a rationale of "provider rate limits " repeated twenty times.
3. Run `uv run pytest tests/unit/cli/test_augment_decision_inject.py` with the source unfixed. Both new tests fail and the clipped notice ends with an ellipsis followed by a period.

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/SKC999/repowise/commit/8bad1a3b210be070743e1a2322e6542bcad7dd5a (the tests commit. Both tests in it fail without the fix commit 7eff3fa).
- **Screenshots/logs:** Failing output from the unfixed run included `...provider rat….'.endswith` in the assertion message for the clipped case. The run ended with `2 failed, 51 passed`.
- **My findings:** The bug reproduces exactly as the issue describes. The reproduction is a failing test and not a manual run of the hook.

**Toolkit**

- Tool: Claude ran the commands in a sandbox for me. The repro-check skill was not used.
- What it produced: Two failing tests that capture the bug and a log of the failure.
- Difficulties: The first dependency install and test run timed out in the sandbox.
- What changed: I reran the install in the background and waited for it to finish.

---

## Solution Approach

**Analysis**

In `_edit_decision_notice` the text called `why` comes from `_clip`. That helper collapses whitespace and cuts to a length with a trailing ellipsis. Rationales are usually written as sentences so they end in a period. The function then adds `because {why}` and an optional sessions suffix and always ends with `line += "."`. That final period lands after the rationale's own period or after the ellipsis.

**Proposed Solution**

Strip trailing sentence punctuation and spaces from `why` before it is appended. Skip the final period when the line already ends in an ellipsis. I am not changing the notice wording or the sessions suffix. I am also not changing the clip length or the dedupe and injection ledger.

**Implementation Plan**

1. Add `why = why.rstrip(".!? ")` before the `if why:` block in `_edit_decision_notice`.
2. Guard the closing period with `if not line.endswith("…")`.
3. Add two tests beside the existing edit notice tests. One covers a rationale ending in a period and one covers a clipped rationale.

I also strip trailing spaces in step 1. The issue names only the three punctuation marks. This is a small addition and it does not change any other behavior.

**Toolkit**

- Tool: Claude helped me trace the cause and write the plan. The plan-check skill was not used.
- What it produced: The cause traced to four lines and a three step plan.
- Difficulties: none met
- What changed: nothing

---

## Testing Strategy

I ran the touched test file with the fix reverted and then with the fix applied. Before the fix the two new tests failed and the output ended with `2 failed, 51 passed`. After the fix the same file showed `53 passed in 18.82s`. I ran `ruff check` on the two changed files and it reported all checks passed.

The notices from the issue now end with exactly one terminal mark. The sentence rationale gives a notice ending in `terminals.` and the clipped rationale gives a notice ending in `…` with no period after it.

I did not run the full `tests/providers/ tests/unit/` suite or the npm lint and type checks locally. I left those to CI and the PR description says so. CI was still pending when I wrote this section.

**Toolkit**

- Tool: Claude ran pytest and ruff in the sandbox. The ship-gate was not used.
- What it produced: Before and after test output and a clean ruff run on the changed files.
- Difficulties: A full local run was too slow in the sandbox.
- What changed: I limited local checks to the touched file and relied on CI for the rest.

---

## Implementation Notes

**November 8, 2026**

I picked issue 5361 after reading three other threads that were already claimed. I read the repo's CONTRIBUTING.md and learned that a claim comment reserves an issue. I posted my claim before writing code.

**November 8, 2026**

I built the fix and two tests in a local clone and confirmed that the tests fail without the fix. I used the same fix and tests when I made the branch on my fork.

**November 8, 2026**

No GitHub login was available in the sandbox so I made the same edits through GitHub's web editor on a new branch in my fork. A stray keystroke added one extra letter to a docstring in the test file. I caught it in the diff preview and removed it before committing.

**November 8, 2026**

I opened the PR and first overwrote the repo's PR template by mistake. I caught that before submitting and rewrote the description to answer every template item and to disclose the AI help.

**Toolkit**

- Tool: Claude Code and the GitHub web editor.
- What it produced: Two commits on a branch in my fork and the PR.
- Difficulties: The web editor made it easy to add stray characters and to overwrite the template.
- What changed: I check the diff preview before every commit and read the PR form before editing it.

---

## Pull Request

**PR Link:** https://github.com/repowise-dev/repowise/pull/5381

**PR Description:** The PR follows the repo template. It explains that the fix strips trailing sentence punctuation and skips the closing period after an ellipsis. It says what is not changed and shows before and after notices. It lists the tests and lint I ran and the ones I left to CI. It says that Claude helped write the fix and tests and that I left the Claude co-author trailer off the commits as CONTRIBUTING asks. The issue also says that open PR 2345 edits the same file so I may need to rebase after it lands.

**Maintainer Feedback:**

- November 8, 2026: none yet

**Status:** Awaiting review

**Toolkit**

- Tool: Claude Code helped draft the description and the GitHub web UI opened the PR. The ship-gate was not used.
- What it produced: PR 5381 against repowise main with two commits.
- Difficulties: I first replaced the PR template text by mistake.
- What changed: I restored the template sections in my description.

---

## Learnings and Reflections

**Technical Skills Gained**

I learned how a clipping helper and a closing punctuation step can combine into a double mark. I learned how to write an async unit test with the repo's `_build_wiki_db` helper. I also learned how to branch and commit on a fork and open a PR from the GitHub web editor.

**Challenges Overcome**

The hardest part was finding an issue that nobody had claimed. Three early candidates were taken and I only saw that by reading the threads. Setting up the project in the sandbox timed out once and I solved it by running the install in the background. I also caught two editing slips in the web editor before they reached the PR.

**What I'd Do Differently Next Time**

I would run the scout and the other course tools instead of working by hand. I would also run the full CI commands locally before opening the PR. I would start earlier so that the tools could be built and tested before the deadline.

---

## Resources Used

- Repo contribution guide with the claim rules: https://github.com/repowise-dev/repowise/blob/main/.github/CONTRIBUTING.md
- The issue thread: https://github.com/repowise-dev/repowise/issues/5361
- The existing tests that I copied the setup from: `tests/unit/cli/test_augment_decision_inject.py` in the repowise repo
