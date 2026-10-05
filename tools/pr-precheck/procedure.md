# Procedure: how this tool grades a PR package

## Read order

1. Read the repo facts (live: PR template, `CONTRIBUTING.md`, AI policy
   file). Write down, as a numbered list, every ask stated as required
   for a PR (template sections, checklist items, issue-link format,
   changelog/whatsnew file, test/lint command) and whether the AI
   policy requires disclosure. Reading these first means the standards
   check later has a fixed list to tick, not an impression.
2. Read the issue and thread highlights. Note any explicit maintainer
   question or direction.
3. Read the plan (plan context; live: `plan.md`) BEFORE the PR. Write
   down: (a) the files and change it names, (b) its not-in-scope line,
   (c) every item it promises, (d) its test plan's expected outcomes,
   one per repro/failure mode, (e) every deviation or deferral note.
   The plan comes before the diff so the diff is judged against what
   was promised, not against the description's story of it.
4. Read the diff hunk by hunk (live: `git diff main...HEAD`), then the
   commit list.
5. Read the test evidence.
6. Read the title and description last.

## Evidence gathering

1. Diff vs plan (for `plan-fidelity` a/b): make a table with one row
   per diff hunk: file, what the hunk does, and the plan item or
   deviation note it implements, or "none". Then list each promised
   plan item from Read order 3(c) and mark it "in diff", "deferred with
   note", or "missing".
2. Description vs diff (for `plan-fidelity` c): quote every sentence in
   the description that claims what the PR does or does not change, and
   next to each, the hunk that confirms or contradicts it.
3. Evidence vs test plan (for `decisive-evidence`): for each expected
   outcome from Read order 3(d), record the command shown, whether a
   before and an after output are shown, and whether the run exercises
   the changed path (the issue's trigger) or a control/unchanged path.
4. Debris scan (for `clean-diff`): list every added or modified line
   that is a debug print/log, commented-out code, a dead function or
   variable, an import or formatting change on an otherwise untouched
   line, or an edit unrelated to the fix.
5. Standards (for `standards-met`): tick each ask from Read order 1
   against the title, description, and diff file list; record "met",
   "missing", or "placeholder only". Record whether a required AI
   disclosure is present.
6. Thread and repo checks (preferred checks): record whether the
   description engages each maintainer direction from Read order 2, and
   whether the repo's named test/lint command's output is shown.

## Check execution

1. Run the four required checks in rubric order, then the two
   preferred checks.
2. Grade each check only from the evidence recorded for it above,
   applying the rubric's pass condition literally; re-read the package
   only if a recorded quote is ambiguous.
3. `plan-fidelity`: fail if any hunk row says "none", any promised item
   is "missing" (deferred with a note in the plan and description is
   fine), or any description claim is contradicted.
4. `decisive-evidence`: fail if any expected outcome lacks a shown
   after-run on the changed path, or the evidence is only a claim.
5. `clean-diff`: fail if the debris list is non-empty.
6. `standards-met`: fail if any stated-required ask is "missing" or
   "placeholder only", or a required disclosure is absent.
7. If the evidence a check needs is genuinely absent from the package
   (not merely unread), grade it `unclear` and name what is missing.
8. Write one evidence line per check: the quote or fact that decided
   it.

## Verdict assembly

1. Apply the rubric's verdict rule: accept only if all four required
   checks are `pass`.
2. Any required `fail` or `unclear` makes the verdict reject.
3. Preferred checks are reported and never enter the verdict.
4. For a reject, name the deciding check in the summary: the first
   failing required check in rubric order (`plan-fidelity`,
   `decisive-evidence`, `clean-diff`, `standards-met`), and quote its
   evidence line.
5. Live mode: after the verdict, run the voice seam from SKILL.md and
   list any broken voice rule; it never changes the verdict.
6. Emit the JSON block last, with all six checks listed.
