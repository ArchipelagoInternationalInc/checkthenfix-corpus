# The paid fix: the second-tab card hold is now released at once
## 2026-11-03 · Builder session (Studio Mac) · branch `builder/paid-fix` · part 4

Follows `2026-11-02-paid-fix-stripe-walkthrough-finished.md` (part 3), which
found that when one document is paid for in two tabs, the second card hold
stayed open until the release sweep, about 45 minutes, and was then given the
wrong reason.

Practice database and Stripe test mode only. Nothing was merged or deployed.
Vercel, DNS, Doppler and the live database were not changed. No live key was
used.

## The fix

When a buyer comes back from paying and their own order has not been settled
yet, the report page now asks the server to settle it, even if it is showing
another order's paid result. The server's rule of one active fix per document
refuses that order, so its hold is released straight away with the reason
"This document already has a fix in progress or paid for, so this payment was
cancelled." That path cannot run the fix or take money. The release sweep now
gives a hold whose fix never started its own reason instead of "took too long".

Two sessions wrote this fix in parallel. The first one pushed was kept; this
session dropped its own copy and kept one extra test, which checks that the
page actually wires the release up.

## Re-test, two tabs, test card

| Step | Result |
|---|---|
| Tab A pays, the fix runs | order `charged`, $19.00 captured |
| Tab B pays afterwards and comes back | order `released` within seconds; Stripe payment canceled, $0 captured |
| What tab B shows | the paid result and its download |
| Charges for the document | one |

Tests: 839 passed, 5 skipped. Lint and typecheck clean.

## Open

- Tab B shows only the paid result. Nothing on the page tells the buyer their
  second hold was cancelled. Owner's choice whether to add a line.
- New buyer wording for the sweep, to review with the rest (D-027).
- Untested: the second tab coming back while the first fix is still running.

## Practice database rows from this test

2 more anonymous documents and 4 more fix orders: 2 `charged` with the test
card, 2 `released` with $0 captured. The ordinary retention window removes them.
