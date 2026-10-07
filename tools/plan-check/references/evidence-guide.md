# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

Where it lives. In an eval bundle the cause is in the candidate plan's Cause or Diagnosis paragraph. The behavior it must explain is in the Repro evidence block under Steps, Control, Artifact, Expected and Actual. Maintainer findings sit in Thread highlights. In live mode the cause is in plan.md under its diagnosis heading and the evidence is the student's posted repro comment on the issue as quoted in the drafts.

What good looks like. The stated cause explains the actual output in the repro and survives every control run. A control that shows the blamed part working (same input without a flag or the same call outside one context) rules the cause out even if the issue or thread guessed it.

## Scope

Where it lives. The plan's Change or Scope paragraph with its In and Out lines and the numbered approach steps. In live mode the scope and approach sections of plan.md.

What good looks like. One bounded change at the site the evidence points to plus the tests for it. Anything else is named as out of scope or deferred. A drive-by rewrite adds migrations, new options, UI work, frameworks or refactors that the reproduced bug does not need.

## Executability

Where it lives. File paths, function names and the approach steps inside the candidate plan. In live mode the files and approach sections of plan.md.

What good looks like. A stranger can open the named file and start the chosen change without asking anything. Phrases like "somewhere", "not sure which layer" or "whichever is easier" mean the core decision is still open.

## Test plan

Where it lives. The plan's Test or Test plan paragraph read against the repro evidence's Steps and Expected lines. In live mode the test plan section of plan.md and the repro steps quoted from the repro comment.

What good looks like. A repro step re-run or a named regression test with a stated expected result for the fixed behavior. "Run the full suite" or "should feel faster" names nothing observable.

## Honesty

Where it lives. The plan's Risk or Unknowns lines and any deviation note. In live mode the risks section of plan.md and the Deviations section filled in after the build.

What good looks like. The plan says what it has not tested or measured and what it will do if that fails. False confidence states an untested cause or outcome as fact. A mid-build deviation is recorded in plan.md under Deviations with what changed and why.

## Comms

Where it lives. The candidate plan comment read against Thread highlights and the Repo facts block's contribution policy line. In live mode the draft comment.md read against the live issue thread and the repo's CONTRIBUTING docs.

What good looks like. When a maintainer gave a direction the comment follows it or names it and explains a departure. When the policy requires AI disclosure the comment says plainly that AI helped. Boilerplate that could sit on any issue ignores both.
