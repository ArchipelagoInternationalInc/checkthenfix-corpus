# The paid fix, Stripe walkthrough finished: five cases run, one finding
## 2026-11-02 · Builder session (Studio Mac) · branch `builder/paid-fix` · part 3

Follows `2026-11-02-paid-fix-stripe-walkthrough-stopped.md` (part 2: case c
passed, a, b, d and e not run because the browser pane was hidden). The owner
put the built-in browser pane on screen, and the rest of the walkthrough ran
in it with the Stripe test card.

**All five cases passed on the thing each one tests.** Case d also found a
gap: when two tabs both pay, the second card hold is not released straight
away. It waits for the release sweep, which does release it.

Practice database and Stripe test mode only. Nothing was merged or deployed.
Vercel, DNS, Doppler and the live database were not changed. No live key was
used. The engine and the app ran locally from the branch under Doppler `dev`,
as in part 1.

---

## Results

| Case | Result | Stripe test mode | `fix_orders` |
|---|---|---|---|
| a. fixable document: held, fixed, charged, download | **PASS** | payment succeeded, $19.00 captured, manual capture | `charged` |
| b. scanned document: hold released, 0 charged | **PASS** | payment canceled, $0 captured | `released`, "We could not fix this document." |
| c. leave the Stripe page without paying: nothing held | **PASS** (part 2) | sessions expired, unpaid, no payment at all | `released`, "Checkout was not completed." |
| d. two tabs on one document: one charge | **PASS, with a finding** | one payment captured ($19.00); the second held, not captured, until the sweep | one `charged`; the other see below |
| e. engine stopped mid-fix, hold released | **PASS** | payment canceled, $0 captured | `released`, "The fix stopped unexpectedly." |

### a. A fixable document

- One-page untagged document, checked, then the Fix button. The Stripe page
  described a hold, not a charge.
- After the test card: the report page showed the fix running, then "Your
  Fixed File Is Ready" with the list of what was checked.
- **Order:** `charged`. The fixed file is stored. veraPDF after the fix:
  compliant, 0 failed rules. Visual difference: 0. One image went on the
  check-by-hand list, marked decorative after the AI's description was
  refused (D-021, D-030), as in part 1.
- **Stripe:** payment `succeeded`, authorised $19.00, captured $19.00,
  capture method manual. The card was charged only after the fix passed.
- **Download:** the download link redirected to a short-lived signed file
  address. The file was a PDF and passed a separate veraPDF run (ua1).

### b. A scanned document

- The scanned page from part 1, checked, then Fix, then the test card.
- The page said the buyer was not charged, that the document could not be
  fixed, and that the hold on the card was released.
- **Order:** `released`, "We could not fix this document."
- **Stripe:** payment `canceled`, $0 captured.

### d. Two tabs on one document

- One document open in two tabs. Fix was pressed in both, and both checkout
  pages were paid with the test card.
- **Only one charge.** The first tab to return ran the fix and captured
  $19.00. Its order is `charged`.
- **The finding.** When the second tab came back, the page saw that the
  document already had a charged fix and showed that result. That is the
  right thing to show. But the second order never reached the step that
  releases a hold, so it stayed `held` with $19.00 authorised on the card and
  nothing captured. Nothing in the app released it until the release sweep.
- **The sweep released it,** about 50 minutes later, with $0 captured. See
  "The release sweep" below. Until then the buyer's card showed a $19.00
  pending hold for a fix they had already received from the other tab.
- Not tested: the second tab coming back while the first fix is still
  running. The code path for that releases the second hold at once, because
  only one active fix per document is allowed.
- **Suggested fix** (not made in this session): when a paid order comes back
  to a document that already has a charged fix, release its hold right away
  instead of waiting for the sweep.

### e. The engine stopped mid-fix

- The fix was started, then the engine process was killed while it ran.
- The app saw the engine fail and released the hold itself, at once. The page
  said the buyer was not charged.
- **Order:** `released`, "The fix stopped unexpectedly."
- **Stripe:** payment `canceled`, $0 captured.
- The sweep run afterwards had nothing to do for this order. The hold was
  released by the app, not by the sweep. That is the better outcome; the
  case as written expected the sweep.

### Extra: the app stopped mid-fix

The case above leaves the app running, so the app releases the hold itself.
To test the sweep as the last line of defence, the app process was killed
while a fix was running instead.

- The order stayed `fixing`, with $19.00 authorised and nothing captured.
  The engine finished its work, but nobody was left to capture the payment.
  That is the safe direction: an unfinished job never charges.
- The release sweep handled it: see below.

## The release sweep

Run once both leftover orders were past the 45-minute wait
(`GET /api/cron/release-holds`, with the cron secret):
`open 2, released 2, repaired 0, needsAttention 0, failed 0`.

| Order | Before the sweep | After the sweep | Stripe after |
|---|---|---|---|
| case d, second tab | `held`, $19.00 authorised, $0 captured | `released` | payment canceled, $0 captured |
| app stopped mid-fix | `fixing`, $19.00 authorised, $0 captured | `released` | payment canceled, $0 captured |

**No test-card hold is left open.** Both orders were given the reason "The fix
took too long, so we stopped it." That is accurate for the stopped fix. For
the case d order it is not: that fix never started. It is a small wording
point that belongs with the case d finding.

## Practice database rows from this test

Across parts 2 and 3: 5 anonymous documents and 8 fix orders. 2 orders are
`charged`, for test-card payments in Stripe test mode. The rest are
`released`, with no payment captured. The ordinary retention window removes
them.

## Still open

- **The engine image is still unbuilt.** This Mac has no Docker. The engine
  ran directly with the image's start command, as in part 1.
- The case d finding above, for the owner to decide on.
