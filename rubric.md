# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check               | Evidence                                              | Pass condition                                                                   | Weight   |
| ------------------- | ----------------------------------------------------- | -------------------------------------------------------------------------------- | -------- |
| Issue is open       | Issue status (repo-facts or issue details)            | Issue status is "open", not closed or locked                                     | required |
| Issue is not stale  | Issue creation date (issue details)                   | Issue created within the last 9 months                                           | required |
| Maintainer active   | Last 5 commit dates on default branch (repo-facts)    | At least one commit in the last 3 months                                         | required |
| Repo is not dormant | Last push date and recent issue activity (repo-facts) | Last push to any branch within 60 days                                           | required |
| Issue is scoped     | Issue title and body text                             | Describes a concrete problem or task, not a broad feature request                | required |
| Issue is unclaimed  | Issue comments section                                | No comment indicates someone is actively working on it or has opened a PR for it | required |

## Verdict rule

<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict, they
rank accepted issues; unclear counts as fail." -->

Accept if all six required checks pass. If any required check fails, reject the issue. Unclear responses count as failures.
