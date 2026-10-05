# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

- Where it lives: eval bundle: the candidate plan's "Cause:" (or
  equivalent) sentence, read against the "Repro evidence" block's
  steps, control runs, and Expected/Actual, and against culprits named
  in "Thread highlights". Live: the cause in `plan.md`, read against the
  student's posted repro comment on the issue and maintainer comments.
- What good looks like: the cause explains the observed result and is
  consistent with every control. A control where the blamed piece
  behaves correctly (same input without the flag, the same operator at
  top level, another error printing in the same build) or an artifact
  showing the fault already present before the blamed step rules the
  cause out, however confident the plan sounds.

## Scope

- Where it lives: eval bundle: the candidate plan's "Change:" section,
  its "In:"/"Out:" or not-in-scope line, and any list of work items.
  Live: the same parts of `plan.md`.
- What good looks like: one change at the site the evidence points to,
  plus tests for it. Extra work (rewrites, migrations, new options or
  UI, CI matrices, "while I'm here") is scope creep unless it is
  explicitly deferred. Honestly scoping down and naming what is
  deferred is good scope, not a gap.

## Executability

- Where it lives: eval bundle: file paths, function names, and the
  approach sentence in the candidate plan's change section. Live: the
  "files I'll touch" and approach parts of `plan.md`.
- What good looks like: a named file or function plus one chosen
  approach, enough that someone could open the file and start. "Somewhere
  in", "X or Y, whichever is easier", "profile first and see" mean the
  real decision has not been made.

## Test plan

- Where it lives: eval bundle: the candidate plan's "Test:" section,
  mapped onto the repro evidence's steps and observed result. Live: the
  test plan in `plan.md`, mapped onto the posted repro comment's
  commands and output.
- What good looks like: the repro (or a named test) re-run with the
  specific result expected after the fix ("at step 3 the color flips",
  "`assert score > 0.5` passes", "exit 0"). "Run the full suite",
  "should feel fast", "nothing else breaks" name no observable outcome
  for this fix.

## Honesty

- Where it lives: the plan's risks/unknowns section, any hedges or
  certainty words throughout the plan and comment, and (after the
  build) the `## Deviations` section of `plan.md`.
- What good looks like: what the evidence does not settle is called an
  unknown ("not yet checked whether X"); nothing unshown is asserted as
  verified. A deviation recorded under Deviations, with what changed
  and why, is honest; a change that exists only in the diff is not.

## Comms

- Where it lives: eval bundle: the "Candidate plan comment", read
  against "Thread highlights" (maintainer direction: isolated culprit,
  patch or test request, settled approach, open/prior PRs) and the
  repo-facts "contribution policy" and template lines. Live: the draft
  comment file, read against the issue's comments and the repo's
  `CONTRIBUTING.md`/AI policy files.
- What good looks like: the comment visibly engages the thread's
  explicit maintainer direction (follows it, builds on it, or explains
  a divergence) and, where the repo's AI policy requires disclosure in
  issues/comments or of all AI use, states the tool and extent. No
  policy, conditions-only policies, and PR-only disclosure duties
  require no disclosure line in the comment.
