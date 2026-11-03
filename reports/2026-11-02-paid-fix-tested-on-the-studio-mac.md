# The paid fix, tested on the Studio Mac (part 1)
## 2026-11-02 · Builder session (Studio Mac) · branch `builder/paid-fix`

The task was to test the paid fix from draft PR 3 on the Studio Mac, using the
practice database and Stripe test mode only. Steps 1 to 4 are done. **Step 5,
the Stripe walkthrough, is not finished.** It stopped partway because the
app's built-in browser would not take card details reliably while its pane
was hidden. This report records what has been run so far. A second report
follows when step 5 is finished.

Nothing was merged or deployed. Vercel, DNS, Doppler and the live database
were not changed. No live Stripe key was used.

---

## 1. Tests, lint, typecheck

| Check | Result |
|---|---|
| `npm test` | 832 passed, 5 skipped, 0 failed (69 files) |
| `npm run lint` (including the claims scan) | exit 0 |
| `npm run typecheck` | exit 0 |

## 2. The fix-orders table on the practice database

| Check | Result |
|---|---|
| Address checked, fingerprint (first 16 hex of sha256 of the host) | `070eddddd526607e`, the practice project |
| Throwaway address, fingerprint | `070eddddd526607e`, the same |
| `public.fix_orders` answers | yes, 0 rows at the start |
| Stripe key in that config | test mode |

The migration was not applied anywhere in this session.

## 3. Engine and app

This Mac has no Docker, so **the engine image was not built here.** The
engine ran directly with the image's own start command, using the same tools
installed on this Mac: veraPDF 1.30.2, Tesseract, Poppler and pikepdf.
`/health` reported veraPDF 1.30.2, matching the pin. The app ran under
`npm run dev`, pointed at the local engine and the practice database.

## 4. The fixing step, run directly with the real AI model

| Document | Pages | Outcome | Reason | veraPDF after | Visual difference |
|---|---|---|---|---|---|
| Canonical sample | 1 | **fixed** | — | compliant, 0 failed rules (6 before) | 0 |
| Untagged agenda | 1 | declined | a table was found; the engine cannot tag tables yet | — | — |
| Scanned page | 1 | declined | no extractable text (a scan) | — | — |
| Second untagged agenda (extra) | 1 | **fixed** | — | compliant, 0 failed rules (6 before) | 0 |

Both fixed files were checked again by a separate veraPDF run (ua1 profile):
PASS.

On both fixed files, the one image went on the buyer's check-by-hand list.
The AI's description was refused, so the image was marked decorative (D-021,
D-030). That refusal is expected behaviour, not a failure. For the Part B
walkthrough, it means the real model's descriptions were refused on both
images seen so far. Headings proposed by the AI were not applied (10 and 11
of them), as D-029 says.

## 5. The paid path in Stripe test mode: stopped partway

| Case | Status |
|---|---|
| a. fixable document: held, fixed, charged, download | **not yet run** (see below) |
| b. scanned document: hold released, 0 charged | not yet run |
| c. leave the Stripe page without paying: nothing held | partial evidence, below |
| d. two tabs, one document: one charge | not yet run |
| e. engine stopped mid-fix, then the sweep releases the hold | not yet run |

What happened on case a:

- On the report page, the Fix button showed the price for a document of 10
  pages or fewer. It opened a Stripe-hosted checkout page marked as a
  sandbox, with the card held, not charged.
- The first checkout page expired after 30 minutes, before payment went
  through. Stripe reports that session as **expired, unpaid, with no payment
  behind it: nothing held.** The order row stays `held` (meaning "awaiting
  payment") until the release sweep closes it as abandoned after 45 minutes.
- A second checkout for the same document was allowed, as designed: an
  unpaid order does not block the document.
- The built-in browser did not reliably type into Stripe's card fields while
  its pane was hidden: the expiry date kept reading as a past date. The
  session is waiting for the pane to be shown.

Rows created in the practice database: 2 anonymous documents and 2 fix
orders, neither paid. The ordinary retention window removes them.

## 6. Open

- Finish cases a to e (this session), then publish part 2.
- **The engine image itself is still unbuilt and untested.** This Mac has no
  Docker. A Docker build, or the Railway build when the owner updates the
  checking machine (B4b), is still owed.
