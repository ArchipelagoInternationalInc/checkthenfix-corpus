# Launch checks: hostile files, quick checks, and paid-fix failure paths (2026-11-17)

Practice database and the test checking machine only. The live database and
the production checking machine were never touched; nothing was merged.
Payments in Stripe test mode only. App branch builder/formal-wording.

## Changes made, each on the owner's yes

- The test checking machine was redeployed with the current branch, then
  moved from the Amsterdam region to US East (Virginia) and redeployed again.
  Its status now reads US East. The production checking machine is still in
  Amsterdam; moving it is on the launch-day list.

## Quick checks (no changes)

| Check | Result |
|---|---|
| Identity, lint, types | PASS |
| Tests | PASS: 1109 passed, 0 failed |
| Canonical document measured locally | PASS: 42 to 100 |
| No made-up scores when the validator cannot run | PASS |
| End-of-session loop installed | PASS |
| Crawlers closed / no internal words, on the public platform address | FAIL: the address answers with a sign-in redirect, so the pages cannot be read. Same as Nov 14 and 16; rerun on launch day |
| End-of-session hook sees the validator path | PASS (from the hook's own logs) |

## Hostile files against the test checking machine

Eight made-up files, each under 1.1 MB: a stream unpacking to 1 GB, a
30,000 x 30,000 image, a 200-inch page, 10,000 blank pages, arrays nested
100,000 deep, a sample cut in half, a sample with a garbage cross-reference
table, and a page set in a font made of random bytes.

| Line | Result |
|---|---|
| Every file ends in a report, a clear refusal, or "did not finish" within 5 minutes | PASS: slowest 29 s |
| Health check answers after every file | PASS |
| No restart loop | PASS: peak memory 2.5 GB of 8 GB |
| Three of the worst at once, then a normal file | PASS: all three reports; the normal file scored as before |
| The site's own limit (10 checks per 10 minutes) | refused clearly when reached |

Timing for "Most reports are ready in under a minute": 4 pages in 6 s,
16 pages in about 14 s. A 507-page file goes to the "too large to check while
you wait" screen (about 36 s, mostly the upload); its background check took
12 s. PASS.

## Paid fix, test mode

| Line | Result |
|---|---|
| A fix turned away after payment (untagged table) | PASS: page says not charged; payment cancelled with $0 captured; order released |
| A fix left open too long, released by the hourly job | NOT REACHED: the page returned too fast to leave the order open; the fix succeeded and was captured in test mode. Retry next time by blocking the return in the browser |

## Findings for a builder

1. Two broken files are reported as "a problem on our side ... try again
   later" instead of "we could not read this file".
2. A real-text page with very little text (under 120 characters) is called
   "a scan", and the fix is refused for that reason.
3. A declined fix does not tell the buyer why (here, a table).
4. While a file is still sending, the homepage sample result shows under it.

## Housekeeping

The Nov 2 refunds report is still not in this repository; the owner was asked
whether he still wants it.
