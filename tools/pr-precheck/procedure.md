# Procedure: how this tool grades a PR package

## Read order

1. Read the repo facts first. In live mode read `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`. Write down every ask the repo states for a PR, one per line. Include template sections, an issue link line, checklists and changelog or whatsnew entries. Write down the AI policy and whether it requires disclosure.
2. Read the issue and its thread highlights. Write down the one behavior the issue says is broken.
3. Read the plan context before anything in the candidate PR. In live mode this is `plan.md` with its Deviations section. Write down four lists. The first is every file the plan names. The second is every deliverable the plan commits to. The third is everything the plan puts out of scope or defers. The fourth is the test plan with each failure case and its expected-after.
4. Read the diff next. Run `git diff main...HEAD` in live mode. Write down every changed file and give each hunk a one-line label saying what it does.
5. Read the test evidence. In live mode this is `test_evidence.md`. Write down each command that was run and the before and after output it shows.
6. Read the commit list.
7. Read the title and description last. In live mode this is `pr_draft.md`. Write down every factual claim about what the PR changes, any fidelity claim, any deviation or deferral note and any AI disclosure.

The plan comes before the diff so the hunks are judged against what was promised. The description comes last so its confidence cannot anchor the reading of the diff or the evidence.

## Evidence gathering

1. For diff-matches-plan, pair each hunk label from read order step 4 with the plan deliverable or deviation note that covers it. Mark any hunk with no partner as unplanned. Then pair each plan deliverable with a hunk. Mark any deliverable with no hunk and no deferral note as missing.
2. For description-faithful, pair each claim from read order step 7 with the hunk that shows it. Mark any claim with no hunk. Mark any fidelity or narrow-scope claim that an unplanned hunk from step 1 contradicts.
3. For repro-before-after, pair each failure case in the plan's test plan with a command in the test evidence. Check that the command runs the failing case and not a control or an unchanged path. Copy the before line and the after line and compare the after with the plan's expected-after.
4. For repo-checks-run, find the suite or checks that the plan's test plan or the repo asks name. Copy the line in the test evidence that reports each one and its outcome.
5. For no-debris, scan every added line and every removed-then-re-added line in the diff. Copy any debug print, commented-out code, dead function, stray TODO, or change that only moves imports, re-indents or reprints identical lines.
6. For template-asks-met, pair each ask from read order step 1 with the place in the title, description or diff that honors it. Mark any ask with no partner. Skip asks a PR package cannot show.
7. For ai-disclosure, use the policy from read order step 1 and the disclosure from read order step 7.
8. For commit-messages, copy any commit message that does not say what it changes.

## Check execution

1. Run the checks in rubric table order from diff-matches-plan to commit-messages.
2. Grade each check only by its pass condition in `rubric.md` using the evidence gathered for it. Do not reread the whole package unless that evidence is missing.
3. For diff-matches-plan, one unplanned hunk or one missing deliverable fails the check. A deviation note in the plan or the description covers the hunk or gap it names. Tests for the planned fix and the imports it needs are planned work.
4. For description-faithful, judge the claim against the diff and never the diff against the claim. A claim of "exactly as planned" fails when step 1 found an unplanned hunk.
5. For repro-before-after, a sentence that says it works is not evidence. Pass only when real before and after output from the plan's failing case is shown. Fail when any failure case the test plan names has no re-run.
6. For repo-checks-run, a stated outcome such as "passes" or "3 passed" is enough. An honestly reported failure with its reason passes.
7. For no-debris, one copied leftover fails the check however correct the fix is.
8. For template-asks-met, pass when the repo states no PR asks. Otherwise one visibly skipped ask fails it.
9. For ai-disclosure, pass when no disclosure is required. Otherwise pass only if the description itself discloses AI help.
10. If the evidence for a check is truly absent from the package, grade it unclear. Never grade unclear when the evidence is present.
11. Record one line of evidence for each grade, quoting the fact or phrase that decided it.

## Verdict assembly

1. Apply the verdict rule in `rubric.md`. Accept only if all seven required checks grade pass.
2. Count an unclear grade on a required check as a fail.
3. Ignore commit-messages for the verdict and mention it in the summary only.
4. For a reject, the deciding check is the first failing required check in rubric table order. Name it and quote its evidence line in the summary.
5. Emit the JSON block from `SKILL.md` last with every check listed in rubric order.
