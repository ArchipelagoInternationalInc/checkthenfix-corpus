# The paid fix, Stripe walkthrough: one case passed, four stopped
## 2026-11-02 · Builder session (Studio Mac) · branch `builder/paid-fix` · part 2

Follows `2026-11-02-paid-fix-tested-on-the-studio-mac.md` (part 1: tests
green, the orders table confirmed on the practice database, the fixing step
run on four documents with two fixed and two declined).

This session went back to the Stripe test-card cases on the owner's word. The
app's built-in browser still did not take input reliably while its pane was
hidden, and this session was not open in any window, so the pane could not be
shown. The instruction for that case was to stop rather than work around it.
**One case passed. Four were not run.**

Practice database and Stripe test mode only. Nothing was merged or deployed.
Vercel, DNS, Doppler and the live database were not changed. No live key was
used.

---

## Results

| Case | Result | Evidence |
|---|---|---|
| a. fixable document: held, fixed, charged, download | **NOT RUN** | The test card could not be entered: the expiry field kept reading as a past date. On the second attempt the Fix button click did not reach the page. |
| b. scanned document: hold released, 0 charged | **NOT RUN** | Needs a paid checkout. The scanned page was checked and scored, so it does offer the Fix button. The fixing step declines it (part 1). |
| c. leave the Stripe page without paying: nothing held | **PASS** | See below |
| d. two tabs on one document: one charge | **NOT RUN** | Needs a paid checkout |
| e. engine stopped mid-fix, then the sweep releases the hold | **NOT RUN** | Needs a paid checkout |

### Case c, in detail

- Two checkout pages were opened for one document and neither was paid.
- **Stripe test mode:** both sessions are `expired`, `unpaid`, with no payment
  intent at all. Nothing was ever held on the card.
- **`fix_orders` before the sweep:** two rows in `held`, meaning "waiting for
  payment". An unpaid order did not block a second checkout, as designed.
- **The release sweep** (`GET /api/cron/release-holds`, with the cron secret),
  run once both orders were past the 45-minute wait:
  `open 2, released 2, repaired 0, needsAttention 0, failed 0`.
- **`fix_orders` after the sweep:** both rows `released`, with the reason
  "Checkout was not completed." No payment was captured or refunded.
- **Without the secret,** the same route answered 404, as designed.

## What was observed along the way

- The Fix button showed the price for a document of 10 pages or fewer. It
  opened a Stripe-hosted page marked as a sandbox, worded as a hold, not a
  charge.
- A Stripe checkout page stops taking payment after 30 minutes, and the sweep
  waits 45. Both behaved as D-028 describes.

## Practice database rows from this test

2 anonymous documents and 2 fix orders, both closed as `released`. No payment
exists behind either. The ordinary retention window removes them.

## To finish cases a, b, d and e

Run them in a browser a person can see. Either the built-in browser pane
open on screen, or Claude in Chrome if the owner prefers. The engine and the
app run locally from the branch, as in part 1, under Doppler `dev`.
