# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request I opened and the evaluation runs that produced `eval-run.txt`.

---

## Your pull request

**Pull request link**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/135

**Branch name**

`fix/1-ingest-skip-check`

It is on my fork https://github.com/SKC999/pathreview-ai301-fa26-s3 and targets `main` on `codepath/pathreview-ai301-fa26-s3`. The PR closes issue #1.

## Eval iterations

**Run history**

1. Full run 1 scored 19/20 with every category matched (`clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`). This is the run saved in `eval-run.txt`.
2. Partial run `--only pkg-05 --out` to read the grader's evidence for the one miss. It scored 0/1 and confirmed the miss came from `repro-before-after`. I changed no files before or after it.
3. Partial run `--only calib-04 --include-calibration` as a trap check for the same check. Calibration packages carry no score. My tool rejected `calib-04` on `repro-before-after` and agreed with the gold label.

The first full run cleared the 18/20 bar and the category floor. So I made no revisions and ran no second full run. The final score is 19/20, which is the score in `eval-run.txt`.

**Package analysis**

I picked `pkg-05` (nushell/nushell#18848). The gold label is accept and my rubric decided reject. It failed only `repro-before-after`.

The plan's test plan names two cases. The first re-runs the issue's script and expects both `atuin` rows. The second re-runs with two same-name same-key bindings and expects one row plus a warning. The test evidence shows the first case with a real before table and after table. The second case is only one sentence. It says "Same-key redefine prints the one-time warning." with no output. My pass condition says "When the test plan names more than one failure case each one is shown." So the grader failed it and wrote that the second case "is only asserted".

I think the gold label is right. The second case is not the bug from the issue. It tests the new warning, a side behavior the plan added. The issue's failing case is shown with a decisive before and after, and the diff adds a test for the warning. My check cannot tell the bug's failing case from an extra case the plan also lists. I left it alone because the bar was met and loosening it would let `calib-04` through.

**Check rationale**

This is the check as it reads in my rubric.md.

| repro-before-after | The test evidence read against the plan's test plan and repro evidence. Find the failing case the repro isolated and the expected-after the plan states. | Pass if the evidence re-runs the plan's own failing repro on the changed code path and shows observable before and after output, and the after matches the plan's expected-after. When the test plan names more than one failure case each one is shown. Fail if the evidence is only a claim ("tested locally", "works now", "verified on my machine"), if it runs only a control or a path the fix does not change, or if a failure case the test plan names is never re-run. | required |

It reads this way because the not-tested packages fail in three different ways. `pkg-04` and `pkg-10` only claim it works ("tested locally", "verified working on my machine"). `pkg-07` and `pkg-14` show real output but from the wrong path. `pkg-07` runs only the single-file control and `pkg-14` runs a GET with header casing that the fix never touches. So the check does not ask whether there is output. It asks whether the output comes from the plan's own failing case on the changed path. The last sentence about every named case is there for `calib-04`. That package re-runs repro 1 and stays silent on the `--exec` abort the plan also named. A separate check (`repo-checks-run`) covers the suite, because `pkg-14` says "All tests pass" and that must never stand in for the repro.

**Trade-offs**

The "each case is shown" sentence costs me `pkg-05`. It is strict, so it rejects an honest PR that shows the issue's case decisively but only asserts a second side case. I keep it because without it `calib-04` passes, and that is a real PR that skips half of what its plan promised to test. I would rather hold a ready PR and ask for one more paste than accept a PR whose plan named a crash that was never re-run. Live mode showed one more gap. The procedure has no fallback when the issue thread cannot be fetched. My live run used the thread quotes in `plan.md` and reported it as a procedure gap. I did not change the procedure, because that would change the files fingerprinted in `eval-run.txt`.

---

Related paths: `eval-run.txt` in this directory, my skill's files in `tools/pr-precheck/`, and the updated `plan.md` in `beat-1-sandbox/unit-3/`.
