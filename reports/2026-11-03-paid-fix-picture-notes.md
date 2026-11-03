# The paid fix: why the picture descriptions are refused
## 2026-11-03 · Builder session (Studio Mac) · branch `builder/paid-fix` · part 7

In part 1, both fixed samples had their one picture's AI description refused,
and the picture was marked decorative and put on the buyer's check-by-hand
list. The fix plan now records which check refused a description. This
session ran the fixing step again on the same two samples and read only that
note: fixed wording and counts, never the description.

Practice database and Doppler `dev` only. No payment was made. Nothing was
merged or deployed. Vercel, DNS, Doppler and the live database were not
changed.

## Tests

`npm test` 841 passed, 5 skipped. Lint and typecheck clean.

## Results

| Sample | Outcome | veraPDF after | Visual difference | The picture's note |
|---|---|---|---|---|
| Canonical sample | fixed | compliant, 0 failed rules | 0 | refused: 265 characters, over the 125-character limit; 2 names or numbers not found on the image |
| Second agenda | fixed | compliant, 0 failed rules | 0 | refused: 273 characters, over the 125-character limit; 2 names or numbers not found on the image |

## What this shows

- **Both descriptions failed the first checks, so the second look never
  decided anything.** A description must pass the length and found-on-the-image
  checks before the second look can confirm it. Neither did.
- **The AI is not told the limit.** The 125-character cap is a setting the
  check enforces, but the request to the model asks only for "concise,
  factual, context-aware" text. Both drafts came back at more than twice the
  limit.
- **"Context-aware" invites words that are not in the picture.** Each draft
  carried 2 names or numbers that text recognition did not find on the
  image itself. They most likely came from the page around it.
- Refusing these is the designed, safe behaviour (D-021, D-030). The cost is
  that, so far, no picture has ever been described automatically.

## Not changed in this session

The prompt and the limit are untouched. Telling the model the limit, and
asking it to describe only what the picture shows, is a decision for the
owner. So is whether to measure the refusal rate on more documents first.
