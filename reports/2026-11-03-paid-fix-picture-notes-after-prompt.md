# Picture descriptions after the prompt change: length mostly fixed, names and numbers still refused
## 2026-11-03 · Builder session (Studio Mac) · branch `builder/paid-fix` · part 8

Follows `2026-11-03-paid-fix-picture-notes.md` (part 7), where both sample
pictures were refused at the first checks: about 270 characters against the
125-character limit, and 2 names or numbers each that were not found on the
image.

The alt-text request was changed in `7970eaa` (by the cloud builder): it
now states the limit, read from the same setting the length check enforces
(`maxImageDescriptionChars`, 125), and tells the model to describe only what
the picture shows, using a name, date or number only if it is printed inside
the picture. The checks, the limit, the second look and the refusal behaviour
are unchanged (D-021, D-030). The model still comes from `JUDGMENT_MODEL`.

Doppler `dev` (practice database) only. No payment. Nothing merged or
deployed. No code changed in this session.

## Checks at the branch tip

Tests 845 passed, 5 skipped. Lint and typecheck clean. The new test asserts
the request carries the configured limit, and that the real pass and the cost
table use the same prompt builder.

## Re-run on the same two samples

The fixing step was run with the real AI model. Only each picture's plan note
was read, never the description itself.

| Sample | Fixed | veraPDF after | Visual difference | Picture note (fixed wording) |
|---|---|---|---|---|
| canonical agenda | yes | compliant, 0 failed rules | 0 | refused: 2 name(s) or number(s) in it were not found on the image |
| second agenda | yes | compliant, 0 failed rules | 0 | refused: description is 133 characters, over the 125-character limit; 2 name(s) or number(s) in it were not found on the image |

| | Before (part 7) | After |
|---|---|---|
| canonical length | 265 | within 125 |
| second agenda length | 273 | 133 |
| names/numbers not found on image | 2 and 2 | 2 and 2 |

## What it means

- **Length:** the stated limit roughly halved the descriptions. One now fits;
  the other is 8 characters over and is still refused, which is the rule
  working as designed.
- **Names and numbers:** unchanged at 2 per picture. From the notes alone it
  cannot be told whether the model still brings in names from the page, or
  whether the words are printed in the picture but text recognition does not
  read them (small or curved lettering, as on a seal). Telling those apart
  means looking at the picture next to the recognised text, which is a
  document-contents task for a private review, not this report.
- Both pictures still go on the buyer's check-by-hand list as decorative after
  refusal. No description was published unchecked.
- The second look still never decided: neither description passed the first
  checks.

## Still open

- The names/numbers refusal above: owner to decide whether to review the two
  pictures privately against the recognised text.
- The engine image is still unbuilt (no Docker on this Mac).
