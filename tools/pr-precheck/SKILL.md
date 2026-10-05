---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package: **is this
pull request ready to submit?** A PR package is a candidate pull
request (its title, description, commits, diff, and test evidence),
read against the plan it claims to implement (including that plan's
recorded deviations), the issue the plan belongs to, and the repo's
stated standards (PR template, contributing asks, AI policy). Do not
grade the plan, the issue, or the code's general quality on their own,
and never grade more than one package per run.

## Inputs and modes

**Live mode** (the student's own PR, before it is opened). Gather:

1. The plan: `plan.md` in the student's working copy, including its
   `## Deviations` section. A house-chain student reads the house plan
   and the house repro pack instead.
2. The diff: run `git diff main...HEAD` (three dots) from the working
   copy on the fix branch; that is everything the branch changes
   relative to the default branch. Also run `git log --oneline
   main..HEAD` for the commit list.
3. The draft PR title and description: the draft file the student
   names (e.g. `pr.md`).
4. The test evidence: whatever the draft description shows (commands
   and output), plus any captured output file the student names.
5. The issue: the issue URL the student gives; fetch its body and
   thread, the repo's PR template (`.github/pull_request_template.md`
   or `.github/PULL_REQUEST_TEMPLATE*`), `CONTRIBUTING.md` (root or
   `docs/`), and any AI policy file, via `gh`, the GitHub API, or the
   web.

Grade what the drafts and the diff actually contain, the way a
reviewer will read the opened PR, not other files in the working
directory.

**Eval mode** (a package bundle). The bundle is the whole world: read
only its text (repo facts, issue, thread highlights, plan context,
candidate PR title, description, commits, diff, and test evidence).
Fetch nothing and read nothing else. Every check runs and the full
verdict rule applies.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. Refuse to grade a
PR whose issue or target repo is outside the repo named on its
`Repo:` line. If that line still carries a bracketed placeholder
(`<ORG>/<PATH-REVIEW-REPO>`), stop without grading and tell the
student to get their cohort's scope file from the instructor; never
guess a scope. Apply its house rules where they change how evidence is
read (e.g. the PR comes from the student's own fork's `fix/` branch,
the template is always used, a classmate's PR on the same issue does
not block). In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` after grading the checks, and hold
the outgoing PR title and description against each of its rules and
its "Things I never post" list. In the summary, quote every rule the
draft breaks and the offending line. The voice guide never changes the
verdict, because no rubric check reads it; voice is the student's own
standard, not a gate. In eval mode, ignore `voice-guide.md` entirely.

## Component reads

- `rubric.md` defines the checks (evidence, pass condition, weight) and
  the verdict rule. It is the only source of checks; never add one.
- `references/evidence-guide.md` maps where each evidence family lives
  in a package (and, live, in the working copy and on GitHub) and what
  good looks like there.
- `procedure.md` is the operating procedure. Execute it as written, in
  order. Where it is silent on a step, do the minimum the rubric's
  pass condition requires and name the gap in the summary under
  "Procedure gaps"; never invent a step silently.
- If `rubric.md` has no filled checks or `procedure.md` has no written
  steps (only template headings and comments), refuse to grade: say
  which file is empty and that this tool cannot grade without it.

## Verdict and output

The verdict is binary: `accept` means ready to submit, `reject` means
hold and fix first. There is no third verdict; reservations go in
evidence lines. Before the block you may print a short per-check
summary (plus voice notes and procedure gaps in live mode). End the
reply with this fenced JSON block, valid, with every rubric check
listed, and nothing after it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first: every grade names the fact or quote that decided it.
  "Looks fine" is not evidence.
- Grade the thing, not the polish: read the diff itself against the
  plan, and the evidence itself against the test plan. A terse PR can
  be ready; a confident description can hide drift.
- The rubric decides: if a check passes by its stated condition but
  feels wrong, it passes, and the tension goes in the summary.
- The procedure decides how: follow `procedure.md` and report its gaps.
- Unclear: a required check graded `unclear` counts as a fail unless
  the rubric's verdict rule says otherwise. A claim you cannot verify
  from the package is not ready to submit.
