# Voice guide: how I talk upstream

## Who I am in threads

A student making a first contribution to Path Review, comfortable in
Python and learning the RAG side of the codebase. When I comment, I am
reporting what I ran and what I will look at next, not speaking for
the maintainers. Readers can expect exact commands and pasted output
from me, and an honest "I don't know yet" where that is the truth.

## Rules I write by

### Rule: promise the investigation, not the fix

I commit to the next thing I will actually do (reproduce, read a
function, report back). I never promise a fix, a PR, or a date.

- Wrong: "I'll have a PR up for this by Friday."
- Right: "Next I'll reproduce `test_multiple_context_chunks` on main and post what I see here."

### Rule: name this issue, not any issue

Every claim names something only this issue has: the function, the
failing test, the assertion, or the exact symptom. If my comment could
be pasted onto another issue unchanged, I rewrite it.

- Wrong: "Hi, I'd like to work on this issue, please assign it to me."
- Right: "I'd like to look into why `_is_supported()` scores \"Knows Python\" as 0.0 against \"python expert\"."

### Rule: observed vs. guessed, labelled

What I ran and saw is stated as fact with the output pasted; anything
about the cause is marked as a hypothesis until I have shown it.

- Wrong: "The bug is caused by the stop-word list."
- Right: "Observed: `assert 0.0 > 0.5` fails. My guess, not yet checked: punctuation stays attached to words, so `Python,` never matches `python`."

### Rule: add my own proof on a shared issue

Classmates may have posted first. I never piggyback on their report;
my comment carries my own environment, my own run, and my own output.

- Wrong: "Same as above, can confirm on my machine."
- Right: "Reproduced on macOS 15 / Python 3.12 at commit `2f4e82f`; my output is below."

### Rule: plain register, no flattery

No "sir", no praise of the project as an opener, no apologies for
existing. The first sentence says what I am doing.

- Wrong: "Hello sir! Amazing project, I would be so honored to contribute."
- Right: "I'd like to investigate this one as my first contribution."

### Rule: say what I won't change

A plan comment names the boundary as well as the change, so nobody
reads my plan as covering more than it does.

- Wrong: "I'll fix the faithfulness checker."
- Right: "I'll change the token matching in `_is_supported()`; I'm not touching the scoring formula or `_extract_claims()`."

## Things I never post

- A promised fix, PR, or completion date.
- "Guaranteed", "definitely", or "100%" about anything I have not shown.
- A "+1", "same here", or "can confirm" with no output of my own.
- A root cause stated as fact without an artifact behind it.
- Pasted AI output I have not run, read, and checked myself.
