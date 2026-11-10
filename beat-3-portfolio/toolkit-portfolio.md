# Toolkit Portfolio

Path: `beat-3-portfolio/toolkit-portfolio.md`

**Student:** Sai Karthikeya Cherukuru
**GitHub Username:** SKC999
**Course repo:** https://github.com/SKC999/ai301-coursework
**Contribution:** https://github.com/repowise-dev/repowise/pull/5381 (issue https://github.com/repowise-dev/repowise/issues/5361)

---

## Overview

I built four graded tools in Beat 1 and installed a fifth in Unit 5. Every one of them is a rubric that a Claude Code skill applies to a package of evidence. The Beat 1 tools passed their evaluation bar three times out of four. The fifth tool, the issue scout, was graded on a real issue for the first time on November 8 and it returned the wrong answer. That failure taught me more than the passing runs did. I did not build the Unit 6, 7 or 8 tools. The Limitations section says what that cost me.

---

## Featured tools

### 1. repro-check (Unit 2, evaluated September 27)

**Toolkit**

- Tool: repro-check. It grades a reproduction package against my rubric and an evidence guide. I ran it live on my claim comment and my repro report for pathreview issue #1.
- What it produced: A 20/20 agreement score against the gold labels with every category matched (`clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`). The bar was 18/20. Live on my claim comment it returned accept on claim-specific and ai-policy-followed.
- Difficulties: On November 8 I pointed it at my repowise reproduction and it refused to grade. Its scope.md limits grading to `codepath/pathreview-ai301-fa26-s3` and it found no issue 5361 there.
- What changed: Nothing in the tool. Staff wrote the scope file so I did not edit it. I reproduced the repowise bug with two failing tests instead and I plan to ask whether other repos should count.

Excerpt from the repro report I posted on September 27 (comment 5862549271). It is the line the tool pushed me to write:

> I reproduced part of this issue. The already-ingested check never works, so re-ingesting the same source runs the full pipeline again and calls the embedding provider a second time. I did not see duplicate vectors in ChromaDB when the content was identical.

The tool rewarded stating what I did not see. That habit carried into my repowise PR description where I listed the checks I left to CI.

### 2. plan-check (Unit 3, evaluated October 6)

**Toolkit**

- Tool: plan-check. It grades a plan package against my rubric, an evidence guide and a procedure file I wrote. It is the gate for the plan I wrote for pathreview issue #1.
- What it produced: 19/20 against the gold labels (`clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`). The bar was 18/20. The one miss was pkg-14, a clear accept that my tool rejected on the grounded-cause check.
- Difficulties: My grounded-cause check is strict enough that it rejected one good plan. I did not loosen it because the four wrong-cause packages all rely on that strictness.
- What changed: I added a Deviations section to plan.md on October 11 so the plan matched what I built. I did not edit the rubric after the run.

The plan this tool governs produced the following build result on October 6. The excerpt is the outcome the plan promised and it is not output from the tool itself.

```text
Before the fix (commit 2f4e82f)
ingest #2: skipped=False chunk_count=3 embed_calls=2 vectors_in_store=3
2 failed, 1 passed, 1 warning in 1.35s

After the fix (commits d94cdda and f3c69cc on fix/1-ingest-skip-check)
ingest #2: skipped=True chunk_count=0 embed_calls=1 vectors_in_store=3
3 passed, 1 warning in 1.35s
```

The plan comment is at https://github.com/codepath/pathreview-ai301-fa26-s3/issues/1#issuecomment-6030137863. The cause was a string passed to `db_session.query()` where a model class belonged. The plan also noticed that the model has no `source_id` column so the one line fix in the issue would still not have matched anything.

### 3. pr-precheck (Unit 4, evaluated October 11)

**Toolkit**

- Tool: pr-precheck. It grades a pull request package against the plan it claims to implement. It is the gate for PR 135 on the course repo.
- What it produced: 19/20 against the gold labels (`clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`). The bar was 18/20. The miss was pkg-05 which my tool rejected on the repro-before-after check.
- Difficulties: My repro-before-after check cannot tell the bug's failing case from an extra case that a plan lists. In pkg-05 the second case only tested a warning the plan added and the tool failed the package for showing it in one sentence.
- What changed: I ran two follow up checks on October 11 and edited no files. A partial run of pkg-05 confirmed the miss came from that one check. A partial run of calibration package calib-04 showed my tool still rejects it correctly. I kept the check as written because loosening it would let calib-04 through.

The check as it reads in my rubric.md:

> Pass if the evidence re-runs the plan's own failing repro on the changed code path and shows observable before and after output, and the after matches the plan's expected-after. When the test plan names more than one failure case each one is shown.

The PR is https://github.com/codepath/pathreview-ai301-fa26-s3/pull/135 and it closes issue #1.

### 4. issue-scout (Unit 5, installed October 20 and graded on November 8)

**Toolkit**

- Tool: issue-scout. It is my Unit 1 issue-select skill with a staff overlay that runs `gh search issues`, ranks the results with my filters and grades the top five with my rubric. I wrote a fit profile and a search strategy with four gh queries and eight ranking filters on October 20.
- What it produced: On November 8 I graded repowise issue 5361 with it after I had claimed the issue and opened PR 5381. It returned reject with five checks passed. The only failure was Issue is unclaimed because my own claim comment and my own PR counted as the claim.
- Difficulties: The unclaimed check cannot tell my account from a stranger's. My scope.md fit profile was still a placeholder so scans had nothing to rank against. A scan I ran earlier was never saved and I lost it when my course API account stopped working so I have no verdicts to show from it.
- What changed: On November 8 I created scan-notes.md in the scout folder with a dated note about the failure. I appended a note to rubric.md saying the unclaimed check must ignore claims and pull requests from SKC999. I also replaced the placeholder in scope.md with a real fit profile. I appended the notes and did not rewrite the original check text. I have not rerun the scout since.

The fit profile I added to scope.md on November 8:

> Language: Python first. Type: real bugs with a small fix and tests beside it. Size: one function or one file. Skip: documentation only issues, issues with open pull requests from other people, and issues that need a new dependency.

Most of this tool's story lives in my installed `~/.claude/skills` folder which no repo tracks. The commit that records the contribution it graded is on the course repo as "Add Assignment 5 contribution story" dated November 8.

---

## Inventory

- issue-select (Unit 1): six required checks in a rubric. It scored 17/20 on September 20 which was one below the 18/20 bar although every category floor held. Its misses were issue-01 and issue-19 on the scoped check and issue-09 on the stale and unclaimed checks. I used it to pick pathreview issue #1. It is the base the scout was built on.
- voice guard (Unit 6): not built.
- repo memory file (Unit 6): not built.
- ship-gate (Unit 7): not built. I ran pytest and ruff on the touched files by hand.
- manifest (Unit 7): not built.
- PR monitor (Unit 8): not installed. I check PR 5381 by hand.

---

## Arc

**September 20.** I wrote my first rubric with six checks and ran the Unit 1 harness. It scored 17/20 and I submitted anyway because every category floor held. I picked pathreview issue #1 because it named the broken function and the files around it.

**September 27.** I posted a claim comment and then a repro report on issue #1. The report said I had reproduced the skip check failure and had not seen duplicate vectors. My repro-check skill scored 20/20.

**October 6.** I wrote a plan that read the model file and found that the issue's suggested one line fix would not match anything. I built the fix on a branch in my fork. plan-check scored 19/20.

**October 11.** I opened PR 135 on the course repo and ran pr-precheck, which scored 19/20. I used two partial runs to understand its one miss and changed nothing.

**October 20.** I installed the scout and wrote a fit profile and a search strategy. I never put the fit profile into scope.md.

**Between October 20 and November 8.** I ran a scan and did not save the output. My course API account then stopped working. This is the point where my toolkit stopped growing. I did not build the Unit 6 or 7 tools and I picked my Beat 2 issue by hand.

**November 8.** I claimed repowise issue 5361 after reading three other threads that others had already taken. I fixed the double period in the decision notice, opened PR 5381 and graded the issue with the scout. The scout said reject because of my own claim. I wrote the patch notes the same day. As of November 8 the PR had no maintainer feedback and CI was pending.

---

## Limitations and what's next

**Limitations**

My toolkit is thinner than the course intended. I built the grading tools in Beat 1 and then stopped building. In Beat 2 I did the contribution mostly by hand with Claude as a helper. I have no hook evidence from Unit 6, no ship-gate evidence from Unit 7 and no monitor evidence from Unit 8. The near misses in my contribution story are the cost of that. I overwrote the repo's PR template in the web editor and I added a stray letter to a docstring. I caught both in the diff preview but a ship-gate would have caught them without my attention.

The tools I did build have narrow reach. Staff scope files limit repro-check and pr-precheck to one course repo so they could not grade my real contribution. The evaluation scores also measure agreement with gold labels on frozen packages. They do not show that a tool helps on a live issue. The one live test of the scout failed on its unclaimed check. Unit 1 also finished below the bar and I never reran its harness after the scout patch.

The record has gaps. The lost scan means I cannot show how the scout ranks real search results. My Unit 5 edits sit in an untracked folder so the repo history cannot confirm them. PR 5381 may have changed since November 8 and this page does not claim any state for it beyond that date.

**What's next**

First I will make the unclaimed check ignore my own account and rerun the Unit 1 harness to see whether the 17/20 holds. Then I will rerun the scout with a saved scan so there is a real read-out to keep. After that I will build a ship-gate that runs ruff and the touched tests and shows the PR template diff before I open a pull request. I will keep checking PR 5381 and rebase if PR 2345 lands first. I will answer any review in my own words.
