# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/90

**Branch**

`fix/59-faithfulness-wording` (from https://github.com/mohtashim-syed/pathreview-ai301-fa26-s3), fixing issue #59.

**pr-precheck verdict on my draft**

This is the final live-mode run of my installed `pr-precheck` on `pr.md` (the title and description), with the branch diff (`git diff main...HEAD`) read against my `plan.md`. It ran after I added the green CI result to the draft and before I pushed that text to the PR body. Two earlier runs on the draft before I opened the PR, the same package without the CI link, also returned `accept`. The first one asked me to show the step-3 script's command and to fix a miscount in my Deviations; I made both fixes and re-ran.

```
{
  "item": "fix/59-faithfulness-wording → codepath/pathreview-ai301-fa26-s3 (issue #59)",
  "checks": [
    {"name": "plan-fidelity", "grade": "pass", "evidence": "Every hunk maps to a plan Scope item (STOP_WORDS moved unchanged, FILLER_WORDS, _content_tokens/_support_ratio, mean in check() with neutral 0.5, 2+ word claim filter, three #59 xfail removals); type-fix commit 44024e7 is recorded as Deviation 3 and restated in the description; no description claim is contradicted by the diff."},
    {"name": "decisive-evidence", "grade": "pass", "evidence": "Repro re-run before/after on the changed path: 'E assert 0.0 > 0.5' → '1 passed' with score=1.0; issue example 0.0 → 1.0 with control 1.0 → 1.0; file '21 passed, 1 xfailed' and unit '378 passed, 50 xfailed' match the test plan."},
    {"name": "clean-diff", "grade": "pass", "evidence": "Diff touches only faithfulness_checker.py and the three xfail decorators; no debug prints, commented-out code, dead code, or import/format churn; the new faithfulness_no_content_claims log follows the module's existing pattern."},
    {"name": "standards-met", "grade": "pass", "evidence": "All template sections filled, 'Closes #59' present, every checklist item addressed; CI green link verified (pull_request run on 44024e7, conclusion success); no AI policy file, but the description discloses Claude Code and its extent anyway."},
    {"name": "engages-thread", "grade": "pass", "evidence": "All 12 comments on #59 come from author_association NONE (classmates); no maintainer question or direction to engage."},
    {"name": "repo-checks-shown", "grade": "pass", "evidence": "Shown output: make test-unit '378 passed, 50 xfailed, 1 warning', make lint 'All checks passed!', black '110 files would be left unchanged', make typecheck 'Success: no issues found in 76 source files'."}
  ],
  "verdict": "accept"
}
```

CI on the opened PR (head `44024e7`): `lint`, `typecheck`, `test-unit`, `test-integration` and `frontend` all passed (https://github.com/codepath/pathreview-ai301-fa26-s3/actions/runs/37262589397).

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run, v1 tool: `agreement: 18/20 scored items  (bar: 18/20: PASS)`, `categories: clear-accept 5/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`. Disagreements: `pkg-02  clear-accept    accept  reject   NO     failed: decisive-evidence, repo-checks-shown` and `pkg-05  clear-accept    accept  reject   NO     failed: decisive-evidence, repo-checks-shown`.
2. Partial run after loosening `decisive-evidence`, `--only pkg-02,pkg-05,pkg-04,pkg-07,pkg-10,pkg-14,pkg-20` (the two misses, all four not-tested packages, and one standards-wall canary): `agreement: 7/7 scored items` (partial; not a bar run).
3. Full confirming run, saved with `--save-run eval-run.txt`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`, `categories: clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`.

**Package analysis**

`pkg-05` (a keybinding-merge fix whose plan widens the merge identity to (name, mode, modifier, keycode) with a replace warning). Gold label: `accept`. My tool in run 1: `reject`, `failed: decisive-evidence, repo-checks-shown`. Only `decisive-evidence` is required; `repo-checks-shown` is preferred and can't change the verdict. Run 3: `accept`.

The grader's evidence for `decisive-evidence` was: "Issue script shown before/after, but the plan's second failure mode (same-name same-key => one row plus warning) has no shown output, only the claim 'Same-key redefine prints the one-time warning'." So the reproduced failure, the issue's script, was re-run before and after on the changed path. What isn't shown is output for a second expected outcome in the plan's test plan: that redefining the same key still collapses to one row and prints the new warning.

My v1 pass condition required the observable result for "the plan's repro (or each repro/failure mode the plan's test plan names)". The grader read "same-key redefine" as a failure mode and failed it. But it isn't something the repro evidence ever reproduced as broken. It's the behavior the fix must keep (plus a new warning), checked by the added tests. The gold note says "repro table before/after shown". The thing a reviewer needs proven, the bug from the issue, is proven. pkg-02 failed the same way over "a successful save still clears". I took the gold label as right: v1 conflated "every outcome the test plan lists" with "every failure the repro reproduced". The revision draws exactly that line.

**Check rationale**

Check `decisive-evidence` (required), as it reads now:

> | decisive-evidence | The test evidence read against the plan's test plan and the reproduction it built on. | The evidence shows the reproduced failure (the issue's trigger that the plan's repro evidence pinned down) re-run against the change, with the observable result shown after the fix (and the before shown or quoted from the repro) matching the plan's expected outcome, on the changed path, not a control or an unchanged path. Secondary expected outcomes in the test plan (a control still behaving, a success case still working, a related variant) may be shown, or covered by a named test the evidence says was added/run. Fails on "tested locally", "works now", "tests pass", or "verified on my machine" with no shown output for the reproduced failure; on evidence that runs only a control or a different path; or when the plan's repro evidence reproduced more than one distinct failure and one of them is never re-run. | required |

Why it reads this way: v1 required shown output for "each repro/failure mode the plan's test plan names", and that failed two clear accepts (pkg-02, pkg-05) whose only gap was un-pasted output for secondary outcomes: a success path still working, a warning on an unchanged path. Neither had ever been reproduced as broken. The not-tested family is about the reproduced failure not being shown fixed, so the check now centres on "the reproduced failure (the issue's trigger that the plan's repro evidence pinned down)". It still fails each way the not-tested packages fail, in their own words:
- "tested locally" or "tests pass" with no shown output (pkg-04's "colors work now, cargo test passes", pkg-10's "verified working on my machine");
- evidence that "runs only a control or a different path" (pkg-07's single-file control, pkg-14's GET with header casing);
- a second reproduced failure "never re-run". I kept that clause on purpose, for packages like calib-04 whose repro pinned two distinct failures.

I rejected the simpler loosening, "the main repro is shown", because it would let a package with two reproduced failures pass on one.

**Trade-offs**

The looser `decisive-evidence` gives up proof of secondary outcomes. A PR can now pass while the "success case still works" or "warning still prints" behavior is only claimed or attributed to a named test, with no pasted output. If that claim is false, this check won't catch it; only a reviewer reading the test, or CI, would. I accept that miss: the alternative held two of seven clear accepts over output a reviewer wouldn't ask for.

Because this loosened a check, I ran canaries before the confirming full run. `--only pkg-02,pkg-05,pkg-04,pkg-07,pkg-10,pkg-14,pkg-20` covered the two flipped packages, all four not-tested packages (the category this change can leak into), and pkg-20 from the 2-package standards-wall category so the category floor couldn't break unseen. All four not-tested packages and pkg-20 stayed `reject` while pkg-02 and pkg-05 flipped to `accept` (`agreement: 7/7 scored items`). The confirming full run then held at 20/20 with every category matched (`clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`), so nothing else moved.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
