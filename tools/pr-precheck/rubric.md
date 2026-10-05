# Rubric: is this pull request ready to submit?

"The plan" means the posted plan the PR claims to implement, together
with its recorded deviation notes (the package's plan-context block in
eval mode; `plan.md` with its `## Deviations` section in live mode).
Locations are defined in `references/evidence-guide.md`.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan-fidelity | Every hunk of the diff read against the plan's change, scope boundary, files, and deviation notes; every planned item read against the diff; every claim the description makes about what the PR does read against the diff. | (a) Every changed file and hunk implements the plan or is covered by a recorded deviation note: no unplanned feature, option, flag, setting, rename pass, rewrite, or edit to a file the plan never touches. (b) Every item the plan promises is in the diff, or its absence is recorded as a deferral/deviation in the plan AND stated in the description. (c) No description claim ("implements the plan exactly", "no other changes", "docs now document X") is contradicted by the diff. An honestly disclosed shortfall passes. | required |
| decisive-evidence | The test evidence read against the plan's test plan and the reproduction it built on. | The evidence shows the reproduced failure (the issue's trigger that the plan's repro evidence pinned down) re-run against the change, with the observable result shown after the fix (and the before shown or quoted from the repro) matching the plan's expected outcome, on the changed path, not a control or an unchanged path. Secondary expected outcomes in the test plan (a control still behaving, a success case still working, a related variant) may be shown, or covered by a named test the evidence says was added/run. Fails on "tested locally", "works now", "tests pass", or "verified on my machine" with no shown output for the reproduced failure; on evidence that runs only a control or a different path; or when the plan's repro evidence reproduced more than one distinct failure and one of them is never re-run. | required |
| clean-diff | The unified diff's hunks and the commit list. | Every hunk is part of the change: no debug prints or logging left in, no commented-out code or earlier attempts, no dead functions/variables (including ones silenced with allow/noqa), no formatting or import churn on lines the change does not otherwise touch, no unrelated drive-by edits. Commit messages alone never fail this check. | required |
| standards-met | The repo-facts PR-template and contributing asks and stated AI policy (live: the repo's PR template, CONTRIBUTING.md, AI policy file), read against the title, description, and diff. Treat every package as AI-assisted work. | Every item the repo's PR template or contributing guide states as required for a PR is honored with real content: required template sections or checklist items filled (not left as placeholders), a required issue link ("closes #N"), and any required file in the diff (e.g. a changelog/whatsnew entry when the guide says fixes need one). If the stated AI policy requires disclosing AI use (in PRs, or "all AI usage in any form"), the description discloses the tool and the extent. Asks the repo does not state are not required. A conditions-only AI policy, or one that only asks for human-written comments, passes when the description reads as the contributor's own words. | required |
| engages-thread | The description read against explicit maintainer direction in the thread (questions asked, approach settled, prior PRs). | Where a maintainer asked a question or set direction that bears on the change, the description engages it. | preferred |
| repo-checks-shown | The test evidence read against the test/lint command the repo's contributing guide names. | The outcome of the repo's own suite or check command is shown, not just claimed. | preferred |

## Verdict rule

Accept (ready to submit) if and only if every `required` check passes.
Any required `fail` holds the PR (reject). A required check graded
`unclear` counts as fail. `preferred` checks never change the verdict;
they are reported for the author.
