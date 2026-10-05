# Rubric: is this reproduction package ready to post?

"The issue's behavior" means the specific symptom the issue describes
(its error type or message, exit code, crash vs. graceful failure, wrong
output), under the trigger the issue describes. Locations named below
are defined in `references/evidence-guide.md`.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| env-recorded | The repro report's environment record (version of the software tested, OS/platform, install method), read against the issue's stated target version and the repo-facts bug-report template asks. | The report names the version of the software it tested and the OS/platform, AND every environment factor the issue or template marks as changing the behavior (e.g. driver, shell, build profile, browser) is recorded. If the tested version or platform differs from what the issue targets (e.g. issue says "latest" or "main", report tests an old release), the report says so explicitly. Fails if there is no environment record, or if the deviation is silent. | required |
| steps-rerunnable | The repro report's steps: commands, inputs, config files, and code snippets, read against the issue's trigger. | A stranger with the recorded environment could re-run the attempt from what is shown plus the issue itself: the commands are given, and each input is either shown, taken explicitly from the issue where the issue states it verbatim ("the exact 12 lines from the issue", "the issue's two calls"), or described precisely enough to recreate unambiguously (a minimal file whose only relevant feature is named). Fails when anything depends on private code, an unshared config, or an unnamed setup ("set up the project", "in our monorepo"), or when the step that performs the issue's trigger is missing. | required |
| behavior-matches-issue | The repro report's artifacts (output excerpts, logs, error text, exit codes, screenshots described), read against the issue's described symptom and trigger. | A shown artifact displays the issue's behavior itself, produced by the issue's trigger: same kind of failure (e.g. a panic/crash vs. a graceful validation error, a runtime error vs. a compile error, the specific wrong output). An artifact that shows an adjacent symptom, a different error, or the program merely running fails, even if the prose says it matches. For a cannot-reproduce report, the artifact shows the actual result of running the issue's trigger. | required |
| claims-match-evidence | Every claim in the claim comment and repro report ("reproduced", "verified", "the cause is", "guaranteed", expected vs. actual), read against the artifacts actually shown. | The package's central claims (that the behavior was reproduced or not, verification, root cause, certainty) are each backed by an artifact shown in the package, and the stated expected/actual agree with what the artifacts show. A secondary observation stated in prose (e.g. a control run described in one sentence, consistent with a maintainer note in the thread) does not fail this check when the central claim is backed. An honest cannot-reproduce passes when it states the outcome plainly and names what differed or was not tested. Fails on claims that go past the evidence (a diagnosis with no transcript, certainty with no artifact, generalizing to versions or platforms not tested). | required |
| claim-specific-honest | The candidate claim comment, read against the issue. | The claim names something specific to THIS issue (its symptom, scenario, version, file, or a thread pointer) and states a concrete next step of investigation. Fails if it is interchangeable boilerplate or a bare "+1"/"assign me", or if it promises an outcome or date it cannot know ("will fix in 2 days guaranteed", "guaranteed reproducible"). | required |
| ai-disclosure | The repo-facts contribution policy, read against the claim comment and repro report. Treat every package as AI-assisted work. | If the stated policy requires disclosing AI use that covers issues or comments (or "all AI usage in any form"), the comments must disclose the tool and the extent of assistance; no disclosure fails. Passes when there is no AI policy, when the policy only sets conditions (understand, test, take responsibility, human in the loop), when its disclosure duty applies only to pull requests, or when it asks for comments in the contributor's own words and the comments read as human-written. | required |
| control-run | The repro report's artifacts. | A control run is shown (same steps with the trigger removed or changed) that isolates the trigger. | preferred |
| template-coverage | The repo-facts bug-report template asks, read against the repro report. | The report supplies every field the repo's bug template asks for. | preferred |

## Verdict rule

Accept (ready to post) if and only if every `required` check passes.
Any required `fail` holds the package (reject). A required check graded
`unclear` counts as fail. `preferred` checks never change the verdict.
In live mode on a claim-only draft, the checks whose evidence is the
repro report (env-recorded, steps-rerunnable, behavior-matches-issue,
and the report half of claims-match-evidence) are `unclear: not yet
applicable` and are left out of the verdict; claim-specific-honest,
ai-disclosure, and the claim half of claims-match-evidence still gate
it.
