# Evidence guide: where proof lives in a reproduction package

The skill uses this guide as its map: for every kind of proof a rubric
check names, it says where to find it in a package and what good looks
like there. In eval mode the package bundle is the whole world; in live
mode the issue side lives on GitHub and the candidate side is the
student's draft files.

## Environment

- Where it lives: eval bundle: the repro report's environment line or
  block (usually opening with "Environment:"), read against the issue
  body's stated version/platform and the repo-facts "bug reports:
  template asks" line. Live: the draft repro comment's environment
  block, read against the issue body and the repo's issue templates
  (`.github/ISSUE_TEMPLATE/`) and setup docs (README, `docs/SETUP.md`).
- What good looks like: the software's exact version (or commit SHA),
  the OS/platform, and the runtime/toolchain versions that matter
  (Python, pytest, browser, driver, shell). Every factor the issue or
  template singles out as behavior-changing is present. When the tested
  version differs from the issue's target, the report says so in a
  sentence ("issue was on 1.10; I tested 1.11.7"); a silent difference
  is a fail even when everything else is recorded.

## Steps

- Where it lives: eval bundle: the repro report's "Steps" list and code
  blocks (commands, config files, input documents, snippets). Live: the
  same parts of the draft repro comment.
- What good looks like: someone starting from a clean checkout of the
  named version could type the commands as shown and reach the trigger.
  Inputs are inline or created by a shown command, not "my project" or
  an unshared config. The step that actually performs the issue's
  trigger (the flag, input shape, operator, or call the issue names) is
  present and matches the issue's trigger, not a modified one. Terse is
  fine; private or skipped is not.

## Behavior shown

- Where it lives: eval bundle: the repro report's output excerpts,
  logs, error text, exit codes, and "Actual:" line, read against the
  issue body's described symptom (and any maintainer note in the thread
  highlights about the trigger). Live: the draft's pasted output, read
  against the live issue body (e.g. the failing test and assertion the
  issue names).
- What good looks like: the artifact itself shows the issue's symptom:
  the same error type or message, the same exit code or crash class,
  the same wrong output. Read the artifact before the prose: a graceful
  validation error is not a panic, a compile error is not a runtime
  error, garbled output with the program still alive is not a crash,
  and "it runs" is not a blank pane. A control run (same steps, trigger
  removed) showing the correct behavior strengthens the case.

## Honesty

- Where it lives: every sentence in the claim comment and repro report
  that asserts an outcome ("reproduced", "confirmed", "verified", "the
  cause is", "guaranteed", "on all versions"), plus the stated
  Expected/Actual, each read against the artifacts shown.
- What good looks like: each assertion points at an artifact in the
  package; nothing is claimed for versions, platforms, or causes that
  were not shown. Expected and actual are stated in the same direction
  as the artifacts. An honest cannot-reproduce says "could not
  reproduce" up front, shows the real attempt's output, and names what
  differed or what was not tested; that is a complete, passing outcome.
  Hypotheses are labelled as hypotheses.

## Comms

- Where it lives: eval bundle: the candidate claim comment (against the
  issue's title, body, and thread highlights) and the repo-facts
  "contribution policy" line (against both comments). Live: the draft
  claim comment, the issue thread, and the repo's `CONTRIBUTING.md`,
  any `AI_POLICY.md`/AI section, and issue/PR templates.
- What good looks like: the claim could only have been written for this
  issue (it names the symptom, scenario, version, file, or a thread
  pointer) and promises investigation, not a fix or a date. No
  flattery-plus-"assign me" boilerplate. AI use: if the policy requires
  disclosing AI use in issues or comments (or in any form), the comment
  states the tool and extent; if the policy only requires disclosure in
  pull requests, only sets conditions (understand, test, take
  responsibility), or asks for human-written comments, no disclosure
  line is required; no policy means no requirement.
