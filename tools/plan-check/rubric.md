# Rubric: is this plan ready to post and build from?

"The repro evidence" means the reproduction the plan builds on: the
package's repro-evidence block in eval mode, the student's posted repro
comment (or the drafts' quotes of it) in live mode. "Maintainer" means
an OWNER, MEMBER, or COLLABORATOR in the thread. Locations are defined
in `references/evidence-guide.md`.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| grounded-diagnosis | The plan's stated cause, read against every step, control run, and artifact in the repro evidence, and against any culprit a maintainer isolated in the thread. | The stated cause explains the reproduced behavior AND no control run or artifact in the repro evidence rules it out (e.g. a control where the blamed component works correctly, or an artifact showing the fault already present before the blamed step runs). Fails if the cause contradicts the evidence, ignores a control that rules it out, or adopts a thread theory the evidence contradicts. | required |
| bounded-change | The plan's change, in-scope and not-in-scope statements, and every piece of work it says it will do. | The work proposed is the change needed to fix the reproduced behavior (plus tests for it). Fails if the plan bundles work the issue did not ask for: refactors, rewrites, migrations or dependency upgrades, new options, features, or UI, framework or CI changes, or "while I'm in there" items. Work the plan explicitly defers or marks out of scope does not count against it; a plan that honestly scopes down to part of the issue and says what it defers passes. | required |
| executable | The plan's change/approach: the files, functions, or code sites named and the approach chosen. | A stranger could start the work without asking the author anything: one approach is chosen, and the place to make it is named concretely enough to open, either a file/function or a specific code path inside a named module or package (e.g. "the reattach path in `zellij-server`'s client connection handling"), even if the exact function is left to pin during the build. Fails if the location is "somewhere" or only a whole layer/subsystem, the approach is a choice left open ("X or Y, whichever is easier", "not sure which layer"), or the plan is only "investigate/profile and then decide". | required |
| decisive-test | The plan's test plan, read against the repro evidence's steps and observed result. | The test plan names an observable outcome that would show THIS fix worked: the repro steps re-run with the specific expected result, or a named test/assertion/measurement for the reproduced behavior. Fails if the only outcome is generic ("run the full test suite", "should feel fast", "nothing else broken", "works now"). | required |
| thread-and-convention | The plan comment, read against the thread highlights (maintainer direction: an isolated culprit, a posted patch or test request, a settled approach, open PRs) and the repo-facts contribution policy. Treat every package as AI-assisted work. | Both: (a) where a maintainer gave explicit direction in the thread, the plan comment engages it: follows it, builds on it, or says why it diverges. Ignoring it (e.g. proposing a docs workaround when the owner isolated the code culprit and asked for testing) fails. No maintainer direction passes (a). (b) If the stated AI policy requires disclosing AI use that covers issues or comments, or "all AI usage in any form", the plan comment discloses the tool and the extent; otherwise (no policy, conditions only, PR-only disclosure, or a human-written-comments rule) (b) passes. | required |
| honest-unknowns | The plan's risks/unknowns and its confidence language, read against the evidence. | Uncertainties the evidence leaves open are named as unknowns rather than asserted as fact. | preferred |

## Verdict rule

Accept (ready to post and build from) if and only if every `required`
check passes. Any required `fail` holds the package (reject). A
required check graded `unclear` counts as fail. `preferred` checks
never change the verdict.
