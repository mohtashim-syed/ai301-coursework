# Rubric: is this a good first issue?

All dates are measured against the bundle's capture date in eval mode and
against today in live mode. "Maintainer" means a commenter or opener whose
association is OWNER, MEMBER, or COLLABORATOR, excluding accounts whose
name ends in `bot` or `[bot]`.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| maintainer-alive | Repo facts: "last 5 default-branch commits" (dates and authors). A bot commit counts only if its message says it merged a pull request from a human account (not from another bot such as dependabot or `*-bot`). | At least one of the last 5 default-branch commits is human-authored or a bot merge of a human's PR, dated within 180 days of the capture date. | required |
| repo-in-use | Repo facts: "archived:" flag on the repo line, "latest release", "last push to any branch". | `archived: no` AND (latest release dated within 365 days OR last push to any branch within 90 days). `archived: yes` always fails. | required |
| not-claimed | Repo facts "this issue: assignees" and "linked PRs" with their states; the Comments section for PRs mentioned in the thread and for claim comments ("working on this", "I'll take this", "I'd like to work on this", "can I get it assigned"). | No assignee; no linked or thread-mentioned PR whose state is open (closed or merged PRs do not block here); and no claim comment, by anyone other than a maintainer, dated within 60 days of the capture date. A claim comment older than 60 days with no open PR is stale and does not block. | required |
| bounded-scope | Issue title and body, and the Comments section. | All of: (a) the issue is not an umbrella, tracking, or "megaissue" whose sub-items are meant to become separate issues or PRs (it links other issues, calls itself a tracker, or invites many PRs), and not an open-ended incremental effort across the codebase ("PRs welcome both big and small"); one bug that names several instances of the same defect in one feature (e.g. "missing several previews: A, B, C") is one fix, not an umbrella; (b) it is not a pure usage/support question; (c) no maintainer says the fix needs changes to core internals or an undecided design, and the thread does not show an unresolved design debate; (d) fewer than 2 closed-unmerged PRs in its history when the issue is more than 1 year old. A short body, missing repro steps, or a maintainer listing named causes and optional extra suggestions does NOT fail this check. | required |
| work-defined | Issue type (bug report vs. feature/enhancement request), opener's association, labels, and maintainer comments in the thread. | Bug reports and documentation fixes pass. A feature/enhancement request passes only if it was opened by a maintainer, OR a maintainer commented in the thread approving it or giving implementation direction, OR it carries a maintainer-applied triage label (`good first issue`, `help wanted`, or equivalent). A feature wish with no labels and no maintainer word fails (the product decision has not been made). | required |
| ai-policy-allows | Repo facts: "contribution policy" line (CONTRIBUTING.md, AI policy files, linked contributor docs). | Fails ONLY on an outright ban on AI-generated or AI-assisted code or documentation (e.g. "we do not accept AI-generated code"). Conditions (disclose AI use, understand and test every change, human review required, AI PRs without human review closed, "strongly discouraged") pass. No stated policy passes. | required |
| maintainer-responsive | Repo facts: "maintainer first-response sample". | At least one sampled issue got a first maintainer comment within 7 days. | preferred |
| friendly-label | Issue labels. | Carries `good first issue`, `help wanted`, `easy`, or an equivalent newcomer label. | preferred |

## Verdict rule

Accept if and only if every `required` check passes. Any required `fail`
rejects. A required check graded `unclear` counts as fail, except
`ai-policy-allows`, where missing policy evidence means no stated policy
and passes. `preferred` checks never change the verdict; they only rank
accepted issues against each other.
