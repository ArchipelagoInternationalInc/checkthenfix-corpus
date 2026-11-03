# Shortening retry: five of six sample pictures now described, every check still applied
## 2026-11-03 · Builder session (Studio Mac) · branch `builder/paid-fix` · part 11

Follows part 10 (`2026-11-03-paid-fix-picture-reader-and-target.md`): after
the seal reader fix, every sample description passed the names check and was
refused only for length (133-146 characters against 125). The owner approved
one shortening retry.

Doppler `dev` (practice database) only. No payment. Nothing merged or
deployed. No database change. Counts and fixed wording only; no document text.

## What changed on the branch

- `7e21996` (cloud builder) added a retry inside the AI step that shortened
  every over-long draft before the names check had run.
- `7c54342` (this session) moved it to where the picture is decided, so it
  follows the approved rule:
  - Only a description refused **for length alone** is sent back. A refusal
    for names or numbers not found on the image, or by the second look, is
    never retried.
  - The shorter version is a new draft: length and found-on-the-image first,
    then the speculation filter, then its own second look, decided exactly as
    a first draft. If it fails anything, the picture is refused as before.
  - Retry count is a config setting (`IMAGE_DESCRIPTION_SHORTEN_RETRIES`,
    default 1). No loop past it.
  - The plan note records it in fixed wording: "described after 1 shortening
    retry", or "...; after 1 shortening retry" on a refusal.
  - The cost table counts the retry at its upper bound (every picture over
    the limit, every retry used).
  - The 125 limit, the matching rule and the refusal behaviour are unchanged
    (D-021, D-030). The model still comes from `JUDGMENT_MODEL`.
- Tests added:
  - an over-long draft followed by a short one is published only after it
    passes every check, second look included;
  - the same with a wrong year in the short one is refused;
  - an over-long draft that stays over-long is refused after exactly one
    retry;
  - a names refusal triggers no retry, and a second-look refusal triggers
    none;
  - the pipeline writes the retry into the plan note.
- Tests 877 passed. Lint and typecheck clean.

## Re-run: both samples, three runs each

All six runs fixed the document: veraPDF compliant, 0 failed rules, visual
difference 0.

| Run | First draft | Shortened to | Picture plan note (fixed wording) | Second look on the shortened text |
|---|---|---|---|---|
| canonical 1 | 176 | 145 | refused: description is 145 characters, over the 125-character limit; after 1 shortening retry | not asked (length failed first) |
| canonical 2 | 135 | 123 | described after 1 shortening retry | confirmed: 6 of 6 claims, confidence 0.99 |
| canonical 3 | 135 | 114 | described after 1 shortening retry | confirmed: 6 of 6 claims, confidence 0.99 |
| second agenda 1 | 139 | 114 | described after 1 shortening retry | confirmed: 6 of 6 claims, confidence 0.99 |
| second agenda 2 | 135 | 114 | described after 1 shortening retry | confirmed: 6 of 6 claims, confidence 0.99 |
| second agenda 3 | 129 | 108 | described after 1 shortening retry | confirmed: 6 of 6 claims, confidence 0.99 |

| | Part 10 | Now |
|---|---|---|
| pictures described | 0 of 6 | **5 of 6** |
| refused for names/numbers not found | 0 of 6 | 0 of 6 |
| refused for length | 6 of 6 | 1 of 6 (after the one retry) |
| shortening calls | n/a | 6 (one per run) |

**The second look now decides.** On the five described pictures it confirmed
the shortened sentence: every claim seen, confidence 0.99. These are the first
real pictures the engine has described with every check passed.

## One point for the owner

On canonical run 1 the second look had already looked at the long first
draft and could not see 1 of its 8 claims. The recorded refusal was for length
alone, because the second look is only consulted once length and names pass,
so the retry rule allowed a shortening. The shorter text would have had its
own second look, but it was still too long and was refused first. Nothing
unchecked was published either way. If the owner prefers, a draft whose
second look has already failed could be excluded from the retry, at no cost
to the five that passed.

## Practice database and servers

The fixing step ran directly, without the app or the engine servers. No rows
were written. This session started no servers.
