# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives. In an eval bundle, the environment record is inside the candidate repro report, usually near the top (a line or block naming OS, versions, install method). The issue's target environment lives in the issue context (the issue body's version and platform fields) and in the thread highlights, where maintainers often confirm "reproduces on latest" or "only on Windows" or "only in release builds". In live mode, the target is in the issue body and comments on GitHub, and the record is in the student's draft repro report.

What good looks like. The report names the OS and the exact version of the software tested (a version string or commit, not "latest"). It also names every factor the issue or thread marks as relevant, such as a driver, shell, terminal, runtime version, or build profile. The tested version matches what the issue targets, or the difference is stated in plain words. A report with no environment record fails even if the output looks right, because nobody can place the attempt. A report that tested an old release against an issue confirmed on latest fails unless it says so.

## Steps

Where it lives. In the candidate repro report's steps or commands section, including any inline files, configs, or input data the steps use. The issue's own trigger lives in the issue body's reproduction steps or example, and sometimes in a maintainer's note in the thread highlights that narrows the trigger.

What good looks like. A stranger with only this report and public resources could reach the trigger. Every command is exact and every input the trigger needs is shown or linked publicly. The steps perform the same trigger the issue describes, using the same syntax, operator, flag, argument form, and input shape. Terse is fine. Steps fail when they skip the triggering action, rely on a private repo or unshared config, or quietly swap the trigger for a nearby one (for example a prefix range instead of an offset-from-end range, a colon instead of an equals sign, or a changed expression that is no longer valid).

## Behavior shown

Where it lives. In the repro report's artifacts, meaning output excerpts, logs, error text, exit codes, stack traces, or described screenshots. Read them against the exact symptom in the issue body and any error text or trace the issue quotes.

What good looks like. The artifact shows the same failure the issue reports. Match the kind of failure, not just the fact that something failed. A panic or crash is not the same as a graceful argument error. A runtime path error is not the same as a compile error. Garbled output with the process still running is not a crash. An artifact that only shows the tool starting, a version banner, or normal operation shows nothing. A control run next to the failing run is strong extra proof. For an honest cannot-reproduce, the artifact shows what actually happened when the issue's trigger was run.

## Honesty

Where it lives. Where the report's claims meet its artifacts, meaning the stated outcome, expected versus actual, any root-cause statement, and any scope claim (other platforms, other versions). Also the claim comment's promises.

What good looks like. Every claim is backed by something shown. "Reproduced" appears only over an artifact that shows the issue's symptom. A cause is offered as a hypothesis unless evidence is shown. Expected versus actual agrees with the shown output. An honest cannot-reproduce passes when it shows a real attempt at the actual trigger, names what differed from the reporter's setup, and suggests what a triggering setup may need. Red flags are certainty words with nothing behind them, a "verified" root cause with no transcript, generalizing the bug to environments the report never tested, and narrating a confirmation over an artifact that shows something else.

## Comms

Where it lives. In the claim comment and the repro report's prose, read against the issue itself and against the repo-facts block in an eval bundle. The repo-facts block states the bug-report template asks and the contribution policy, including any AI-use policy. In live mode, the policy lives in the repo's CONTRIBUTING file, AI policy file, issue templates, and the scope.md house rules.

What good looks like. The claim comment is specific to this issue, meaning it names the behavior and could not be pasted on another issue unchanged. It states a concrete next step the author controls, such as reproducing on a named version and posting the report. It promises nothing the author cannot guarantee, such as a fix, a fix deadline, or a known cause before reproducing. On AI use, treat every package as AI-assisted work. If the repo's policy requires disclosing AI use, one of the comments must say so explicitly, and a missing disclosure fails even when the reproduction is excellent. If the policy only forbids unreviewed or bot-generated comments, specific and human-voiced comments satisfy it. If the policy is permissive or silent, no disclosure is required.
