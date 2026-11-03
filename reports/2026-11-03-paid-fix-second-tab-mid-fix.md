# The paid fix: a second tab returning mid-fix is also released at once
## 2026-11-03 · Builder session (Studio Mac) · branch `builder/paid-fix` · part 5

Follows `2026-11-03-paid-fix-second-tab-hold-fixed.md` (part 4), which re-tested
the second-tab fix with the first tab already charged and left one case open:
the second tab coming back while the first tab's fix is still running.

Practice database and Stripe test mode only, test card. Nothing was merged or
deployed. Vercel, DNS, Doppler and the live database were not changed.

## Result

| Step | Result |
|---|---|
| Tab A pays; its fix starts | order `fixing`, card held, nothing captured |
| Tab B pays and comes back while A is still fixing | order `released` within seconds; Stripe payment canceled, $0 captured |
| What tab B shows | "You Were Not Charged", with the reason that the document already has a fix in progress or paid for |
| Tab A's fix finishes | order `charged`, $19.00 captured |
| Charges for the document | one |

No code change was needed for this case. The page already asks the server to
settle a returning payment while a fix is running, and the server's rule of
one active fix per document releases the second hold.

Tests on the branch tip: 841 passed, 5 skipped. Lint and typecheck clean.

## Open

- A new on-page line for the already-charged case (added by another session
  after part 4) has draft wording for the owner and has not yet been seen in a
  browser.

## Practice database rows from this test

1 more anonymous document and 2 more fix orders: 1 `charged` with the test
card, 1 `released` with $0 captured. The ordinary retention window removes them.
