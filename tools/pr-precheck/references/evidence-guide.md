# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

Where it lives. In an eval bundle the plan's promises are in the Plan context block. Look at its Scope sentence, its Files line, its Not in scope line and any deferral or deviation note. What actually changed is in the candidate PR's Diff and Commits. The description's fidelity claims are in the candidate PR's Description. In live mode the plan is `plan.md` with its Scope, Files, Approach and Deviations sections. The diff is `git diff main...HEAD` from the top of the working copy. The description is everything under the first line of `pr_draft.md`.

What good looks like. Every hunk in the diff does work the plan or a deviation note names, and every deliverable the plan promises is in the diff or deferred in a note. The description's claims match the diff in both directions. Silent drift looks like a new flag, setting, rename pass or rewrite with no note, or a planned docs update that never arrives. It is worst under a description that says "exactly as planned".

## Test evidence (harness category: not-tested)

Where it lives. In an eval bundle the proof is the candidate PR's Test evidence section, sometimes with a before and after in the Description. It is read against the Plan context's Test plan sentence and its Repro evidence. The repro names the failing case and the control. In live mode the proof is `test_evidence.md` read against the Test plan section of `plan.md`. It holds the repro script re-run on `main` and on the branch plus the output of `make test-unit`, `make test-integration`, `make lint` and `make typecheck` from the PR template.

What good looks like. The plan's own failing case is re-run and shows the before output and the after output, and the after matches the expected-after the plan stated. Every failure case the test plan names is covered. The repo's suite or checks show an outcome. "Tests pass" or "works on my machine" with nothing observable is not proof. A re-run of the control or of a path the fix does not touch is not proof either.

## Diff quality (harness category: unreviewable)

Where it lives. In an eval bundle the change is the candidate PR's Diff and the Commits list. In live mode it is `git diff main...HEAD` and `git log main..HEAD --oneline`, with `git diff main...HEAD --stat` for the file list.

What good looks like. Every changed line serves the fix, its tests or an import the fix needs, so a reviewer sees the fix at a glance. The debris tells are a debug print or log line (even commented out), a commented-out first attempt, a dead helper nothing calls, a stray TODO, import reshuffles, re-indents and blocks of identical lines removed and re-added. Commit messages like "wip" or "misc cleanups while debugging" often point to them.

## Standards and comms (harness category: standards-wall)

Where it lives. In an eval bundle the repo's asks are in the Repo facts block on the "pull requests" line and the "contribution policy" line, which includes any AI policy. They are honored or not in the candidate PR's Title, Description and Diff. A changelog or whatsnew entry shows up as a file in the Diff. In live mode the asks are in `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` in the working copy. Path Review's template has Summary, Issue with "Closes #", Changes, Testing with its checkbox list, Screenshots / Demo and Notes for Reviewers. The course also requires an AI-use disclosure, which goes in Notes for Reviewers.

What good looks like. Every section the template names holds real content and every stated ask is visibly met. That means an issue link line where asked, checklist items answered, a changelog entry in the diff where asked and an AI disclosure in the description when the policy requires one. A skipped checklist, a missing "closes #" line or a missing disclosure under a stated AI policy is a visibly ignored ask. Whether the description's claims match the diff belongs to plan fidelity above.
