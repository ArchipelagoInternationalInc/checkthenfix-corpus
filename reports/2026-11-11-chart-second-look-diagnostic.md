# Why the chart's second look refuses: it lists printed names and numbers as guesses
## 2026-11-11 · Builder session (Studio Mac) · branch `builder/formal-wording` · part 4

Follows `2026-11-11-people-pictures-sign-words.md`. This was a private,
read-only diagnostic. Doppler `dev` only. No payment. No code, settings or
database change; nothing merged or deployed. This report gives counts and
categories only. The raw answers were shown on the operator's screen and are
not stored anywhere.

## Method

- **The call:** the fixer's own second-look call, with the same instruction
  and the same image extracted from the test PDF by the engine.
- **The drafts:** three chart descriptions of the kind seen in the runs (two
  first drafts, and one with the years removed), two calls each, so 6
  answers.
- **What was printed:** the raw "guesses", "people" and not-visible claims
  from each answer.

## Results

| | Count |
|---|---|
| Answers | 6 |
| Every claim marked visible | 6 of 6 |
| "people": false | 6 of 6 |
| Confidence 0.95 or higher | 6 of 6 |
| Guesses list empty (confirmed) | 1 of 6 |
| Guesses list not empty (refused) | 5 of 6 |

- **Every listed guess was a name or a number from the description.** None
  was about a person, and none judged looks.
- **Most were printed on the chart:** the chart's title words and two
  values. The text reader also finds these on the image.
- **Two were not read by the reader:** years printed sideways on the axis.
- **The model is inconsistent:** the same draft came back confirmed on one
  call and refused on the other.
- **Even the shorter draft was refused.** It had no years in it, and both
  calls still listed the chart's title as a guess.

## What this means

The instruction asks the second look to list names and numbers that are
**not** printed in the picture. On this chart, it lists printed ones too.
Because any guess at all refuses, the chart is refused even when everything
else passes. It is also reported with the fixed reason "a guess about a person
or a judgment of looks", which is misleading here.

The names check already decides exactly this question, with an exact match
against the text read off the image (D-021).

## Suggested fix (waiting on the owner)

A guess that is a name or number would no longer refuse by itself in two
cases:

- the text reader finds it on the image (an exact match, as the names check
  already requires);
- the names check has already refused it as not found. The retry may then
  remove it, and the shorter text gets its own second look.

Any other guess still refuses. The second look's guesses would stay in
memory only and never reach a note or a log.
