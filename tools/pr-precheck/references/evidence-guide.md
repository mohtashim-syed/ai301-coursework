# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

- Where it lives: eval bundle: the "Plan context" block (its change,
  files, not-in-scope line, test plan, and any deviation/deferral
  note), read against the candidate PR's "Diff" hunks and the
  "Description". Live: `plan.md` including `## Deviations`, read against
  `git diff main...HEAD` and the draft description.
- What good looks like: every hunk maps to a plan item or a recorded
  deviation; every promised item appears in the diff or is deferred in
  the plan AND restated in the description; nothing the description
  claims ("exactly as planned", "no other changes", "docs updated") is
  contradicted by the diff. Drift runs both ways: extra options, flags,
  renames, or files the plan never names (more than the plan), and a
  promised item silently missing or claimed but absent (less than the
  plan). An honest, recorded shortfall is fidelity, not drift.

## Test evidence (harness category: not-tested)

- Where it lives: eval bundle: the candidate PR's "Test evidence"
  section (and any evidence in the description), read against the plan
  context's test plan and repro evidence. Live: the evidence pasted in
  the draft description and any captured output the student names,
  read against `plan.md`'s test plan and the posted repro comment.
- What good looks like: the plan's repro command(s) shown re-run on the
  branch, with the observed output after the fix and the before (shown
  or quoted from the repro), matching the plan's expected outcome, and
  covering every failure mode the test plan names. The run exercises
  the issue's trigger, not a control that never broke or a different
  path. "Tests pass", "works on my machine", or "verified for a day"
  with no output is not evidence. The repo's own suite or check output
  shown is a plus.

## Diff quality (harness category: unreviewable)

- Where it lives: eval bundle: the candidate PR's "Diff" (every hunk,
  including context lines marked `-`/`+`) and "Commits" list. Live:
  `git diff main...HEAD` and `git log --oneline main..HEAD`.
- What good looks like: the fix is visible and is the only thing
  there. Debris tells: `print`/`eprintln`/`console.log`/`DEBUG` lines
  added; commented-out code or a commented-out first attempt; dead
  functions or variables (often behind `allow(dead_code)` or `noqa`);
  re-indented or re-ordered lines and import restructures the fix does
  not need; duplicated or re-printed blocks; edits to unrelated code.
  "wip"-style commit messages are a smell but not by themselves debris.

## Standards and comms (harness category: standards-wall)

- Where it lives: eval bundle: the repo-facts "pull requests:" line
  (template sections, checklist, issue-link format, changelog/whatsnew
  asks) and "contribution policy" line (AI policy), read against the
  title, description, and diff file list. Live: the repo's
  `.github/pull_request_template.md`, `CONTRIBUTING.md` (root or
  `docs/`), any AI policy file, and the issue thread, read against the
  draft title and description.
- What good looks like: each stated-required ask is visibly honored:
  template sections present with real content, checklist items
  addressed, "closes #N" with the real number, the required
  changelog/whatsnew file in the diff when the guide requires one for
  fixes. Where the AI policy requires disclosure, the description names
  the tool and the extent of assistance. Asks the repo does not state
  are not required. Explicit maintainer direction in the thread is
  engaged rather than ignored.
