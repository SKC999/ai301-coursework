# Procedure: how this skill grades a plan package

## Read order

1. Read the repo facts first. Write down the contribution policy and whether it requires AI disclosure.
2. Read the issue next. Write down the one behavior the reporter says is broken.
3. Read the thread highlights. Write down every explicit direction a maintainer or owner gave, such as a chosen approach, a rejected approach, an isolated culprit file or a request to test something. Write "none" if there is none.
4. Read the repro evidence before the plan. Write down the actual behavior, the expected behavior and every control run or isolating step along with what it rules in or out.
5. Read the candidate plan last of the context. Write down its stated cause, every piece of work it commits to, the files or functions it names and its test plan.
6. Read the candidate plan comment. Write down any AI disclosure and anything it says about the thread's direction.

The repro evidence comes before the plan so the grader judges the plan's cause against the evidence and is not anchored by the plan's confidence.

## Evidence gathering

1. For grounded-cause, take the cause sentence from the plan and the control runs and isolating steps from the repro evidence. Also note any maintainer finding in the thread about the culprit.
2. For bounded-scope, list every distinct piece of work the plan commits to from its change, approach and in-scope parts. Put deferred or out-of-scope items in a separate list.
3. For executable, list the files, functions or code sites the plan names and the approach it picked. Copy any phrase that leaves a choice open, such as "somewhere", "maybe", "not sure" or "whichever".
4. For decisive-test, copy the test plan and note each expected result it states.
5. For thread-direction, use the directions written down in read order step 3 and copy any sentence in the plan or comment that refers to them.
6. For policy-disclosure, use the policy from read order step 1 and the disclosure from step 6.
7. For honest-unknowns, copy the plan's risks or unknowns and compare them with what the repro evidence did not test.

## Check execution

1. Run the checks in rubric table order from grounded-cause to honest-unknowns.
2. Grade each check only by its pass condition in rubric.md using the evidence gathered for it. Do not reread the whole package unless that evidence is missing.
3. For grounded-cause, ask whether any control or step in the repro evidence shows the blamed component working or points the failure elsewhere. If yes the check fails even when the plan sounds confident or matches the issue's own guess.
4. For bounded-scope, test each listed piece of work with one question. Would the reproduced bug stay fixed and tested without it? If yes and it is not deferred the check fails.
5. For executable, fail if the plan names no location or no single approach or if a copied open-choice phrase covers the core change. An open question about a side detail does not fail it.
6. For decisive-test, pass if at least one test names a concrete expected result for the fixed behavior. Extra vague tests next to a decisive one do not fail it.
7. For thread-direction, pass when the thread holds no explicit direction. Otherwise pass only if the plan or comment follows that direction or names it and explains a departure.
8. For policy-disclosure, pass when no disclosure is required. Otherwise pass only if the comment itself discloses AI help.
9. If the evidence for a check is truly absent from the package, grade it unclear. Never grade unclear when the evidence is present.
10. Record one line of evidence for each grade, quoting the fact or phrase that decided it.

## Verdict assembly

1. Apply the verdict rule in rubric.md. Accept only if all six required checks grade pass.
2. Count an unclear grade on a required check as a fail.
3. Ignore honest-unknowns for the verdict and mention it in the summary only.
4. For a reject, name the first failing required check in table order and quote its evidence line in the summary.
5. Emit the JSON block from SKILL.md last with every check listed in rubric order.
