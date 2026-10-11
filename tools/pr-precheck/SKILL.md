---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You answer one question about one PR package. Is this pull request ready to submit?

A PR package is a candidate pull request read against two things. The first is the accepted plan it claims to implement, including any deviation notes. The second is the issue that plan belongs to, with its thread and the repo's stated standards. The pull request itself has five parts. They are the title, the description, the commit list, the diff and the test evidence.

Do not answer any other question. Do not review code style for its own sake. Do not judge whether the plan was a good plan, because the plan was already accepted. Do not grade more than one package in a run. Never answer from gut feel. You answer by running `procedure.md`, which applies the checks in `rubric.md` to evidence located with `references/evidence-guide.md`.

## Inputs and modes

You run in exactly one of two modes. Decide which one before reading anything else.

**Live mode** is the student checking their own pull request before it goes out. The student names their issue URL and the files that hold the package. Read these inputs and nothing else as the package.

1. The plan is `plan.md` in the working copy, with its `## Deviations` section. A deviation note there is part of the plan.
2. The diff is every committed change the branch makes against the repo's default branch. Produce it by running `git diff main...HEAD` (three dots) from the top folder of the working copy. Run `git diff main...HEAD --stat` and `git log main..HEAD --oneline` too for the file list and the commit list. Uncommitted changes are not part of the diff.
3. The title and description are in `pr_draft.md`. The first line is the title. Everything under it is the description.
4. The test evidence is in `test_evidence.md` next to `plan.md`.
5. The issue side comes from the real repo. Read the issue and its thread with `gh issue view <number> --repo <repo> --comments` or the GitHub web page. Read the repo's PR template at `.github/PULL_REQUEST_TEMPLATE.md` and its contribution rules at `docs/CONTRIBUTING.md` in the working copy.

A student on the house chain reads the house plan and the house repro pack in place of their own plan and repro. The same checks grade the same things there.

Files in the working copy that are not named above are not the package. Do not grade `comment.md` or any scratch script. If a file you need is missing, say which one and grade the checks that need it as unclear.

**Eval mode** is grading a package bundle such as `pkg-07.md`. The bundle is the whole world. Every fact comes from its text. Do not fetch anything, run git or open any other file. The repo facts block stands in for the PR template and contribution docs. The plan context block stands in for `plan.md`. The candidate PR block holds the title, description, commits, diff and test evidence. Eval mode always grades a complete package with every check and the full verdict rule.

## The scope seam (live mode only)

In live mode read `scope.md` in this skill directory first, before any other file. Find the `Repo:` line.

If the `Repo:` line still holds a bracketed placeholder such as `<ORG>/<PATH-REVIEW-REPO>`, stop without grading. Tell the student to fill the `Repo:` line in `scope.md` with their section's Path Review repo. Do not emit a verdict. Never guess a scope.

If the issue URL the student gives is not in the scoped repo, refuse to grade and say which repo the scope allows.

Apply the house rules in `scope.md` while reading evidence. The pull request comes from a `fix/<issue-number>-<slug>` style branch on the student's fork. It delivers one plan for one issue. The template is always used and every section gets real content. A classmate's pull request on the same issue does not count against this one.

In eval mode ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode read `voice-guide.md` after `scope.md`. Hold the draft title and description from `pr_draft.md` against each rule in it. In the summary list every rule the draft breaks, quote the rule and quote the line that breaks it. If the draft breaks no rule say so in one line.

The voice guide never changes the verdict on its own. Voice is the student's personal register and carries no gold label. Only a rubric check can change the verdict.

In eval mode ignore `voice-guide.md` entirely. Universal communication checks live in the rubric.

## Component reads

Read the components in this order and use each for its one job.

1. `rubric.md` defines the checks and the verdict rule. Each row gives a check name, the evidence to gather, a pass condition and a weight. Required checks gate the verdict. Preferred checks never change it.
2. `references/evidence-guide.md` is the map. It says where each evidence family lives in an eval bundle and in a live working copy, and what good looks like there.
3. `procedure.md` is the operating procedure. Execute it as written, step by step. It decides the read order, how evidence is gathered, how each check runs and how the grades become a verdict.

When the procedure is silent on a step you need, do not improvise around it. Take the narrowest reading the rubric supports and name the gap in your summary as a procedure gap.

If `rubric.md` has no filled check rows or no verdict rule, or `procedure.md` has no filled steps, stop and say so. Do not grade and do not invent checks. A tool without its rubric and procedure cannot grade by design.

## Verdict and output

The verdict is binary. `accept` means the pull request is ready to submit. `reject` means hold it and fix what failed. There is no third verdict and no score. Reservations go in the evidence lines of the checks and never in the verdict.

Before the JSON block print a short readable summary. Give one line per check with its grade and the deciding fact. For a reject name the deciding check that the procedure picks. In live mode add the voice guide notes after the checks.

End the reply with the fenced JSON block below. List every rubric check in rubric order. Make it valid JSON. Put nothing after it.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

In live mode the item is the issue URL until a PR URL exists. In eval mode the item is the bundle id such as `pkg-07`.

## Grading discipline

Grade every check on evidence. Name the fact or quote that decided it. "Looks fine" is not evidence.

Grade the thing and not the polish. A terse pull request can be ready. A confident and beautifully formatted one can hide drift. Read the diff itself against the plan, the evidence itself against the test plan and the description itself against the diff. Never trust the description's own claims about the diff.

The rubric decides. If a check passes by its stated condition but feels wrong, it still passes. Note the tension in the summary if you want. The fix belongs in the rubric and not in the run.

The procedure decides how. Follow `procedure.md` as written and report its gaps instead of filling them.

Treat `unclear` the way the rubric's verdict rule says. Where the rule is silent an unclear grade on a required check counts as a fail. A claim you cannot verify from the package is a failing claim. A pull request you cannot verify is not ready to submit.

An honest shortfall is not a failure. A pull request that does less than everything can be ready when the plan or its deviation notes record the gap and the description says so too.
