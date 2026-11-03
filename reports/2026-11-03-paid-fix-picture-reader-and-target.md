# Seal reader fixed, length still blocks: names now found on every run, every description still too long
## 2026-11-03 · Builder session (Studio Mac) · branch `builder/paid-fix` · part 10

Follows part 9 (`2026-11-03-paid-fix-picture-names-compared.md`), which found
that every refused name or number was printed in the picture and missed by the
text reader, and that descriptions ran just over the 125-character limit. The
owner approved both proposals.

Doppler `dev` (practice database) only. No payment. Nothing merged or
deployed. No database change. Counts and fixed wording only; no document text.

## What changed on the branch

- **Seal reader** (`d49e456`, cloud builder): each flattened ring strip is
  read in sparse-text mode as well as the single-line mode. The text found on
  the picture is the union of both. Matching stays exact.
- **Target length** (`d3ffdcf`, this session): the length the request asks
  for is now its own setting, `IMAGE_DESCRIPTION_TARGET_CHARS` (100), separate
  from the 125-character check, which is unchanged. The prompt reads both from
  config, and it never asks for more than the limit.
- **Tests added:** a ring strip whose words come back only in sparse mode is
  now found, and was missed with single-line mode alone. A wrong name and a
  wrong year are still refused against the same reading. On the real seal the
  inner arc is now read, and a wrong year beside it is still refused. The
  prompt carries the configured target.
- Second look, refusal behaviour and matching rule unchanged (D-021, D-030).
- Tests 857 passed, 0 skipped. Lint and typecheck clean.

## Re-run: both samples, three runs each

All six runs fixed the document: veraPDF compliant, 0 failed rules, visual
difference 0.

| Run | Picture plan note (fixed wording) | Second look |
|---|---|---|
| canonical 1 | refused: description is 146 characters, over the 125-character limit | not confirmed: "second look could not see 1 of 8 claims" (confidence 0.95) |
| canonical 2 | refused: description is 135 characters, over the 125-character limit | confirmed: 6 of 6 claims, confidence 0.99 |
| canonical 3 | refused: description is 135 characters, over the 125-character limit | confirmed: 6 of 6 claims, confidence 0.99 |
| second agenda 1 | refused: description is 144 characters, over the 125-character limit | confirmed: 6 of 6 claims, confidence 0.99 |
| second agenda 2 | refused: description is 140 characters, over the 125-character limit | confirmed: 6 of 6 claims, confidence 0.99 |
| second agenda 3 | refused: description is 133 characters, over the 125-character limit | confirmed: 6 of 6 claims, confidence 0.99 |

| | Part 9 (before) | Now |
|---|---|---|
| runs refused for names/numbers not found on the image | 6 of 6 | **0 of 6** |
| runs refused for length | 6 of 6 (129-135 characters) | 6 of 6 (133-146 characters) |
| pictures described | 0 | 0 |

## What it means

- **The reader fix works.** No description was refused for a name or number
  on any run. Every name and number the AI used is now found on the picture.
- **The shorter target did not work.** Asked for about 100 characters, the AI
  wrote 133 to 146, slightly longer than before. Length is now the only reason
  any picture is refused.
- **The second look ran on all six, but did not decide.** It runs before the
  first checks are applied and is only consulted when they pass, so its answer
  did not change any outcome. It would have confirmed five of the six
  descriptions; the sixth had one claim it could not see. Both pictures still
  go on the buyer's check-by-hand list as decorative after refusal.

## Proposal for the owner (not made)

One retry when a description runs over: send the description back to the AI
with the limit and ask for a shorter version of the same sentence, then put
the shorter version through every check again, including the second look.
The limit stays 125 and nothing over it is published. If the retry is still
too long, the picture is refused as now.

## Practice database and servers

The fixing step ran directly, without the app or the engine servers, so no
rows were written. No servers are running.
