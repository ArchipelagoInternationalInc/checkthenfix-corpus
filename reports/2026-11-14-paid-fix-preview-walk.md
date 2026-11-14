# Paid fix on the preview, Stripe test mode: 3 of 3 pass (Nov 12 to 14, part 2)

Follows `2026-11-12-paid-fix-preview-prep.md`. Branch `builder/formal-wording`
at 60b1b30, code unchanged. Stripe TEST mode only. Nothing merged; the
production site and the production engine were not changed; no database
change. Sample PDFs from the repository's corpus only.

## Results

| Test | Outcome |
|---|---|
| A. fixable 1-page agenda | PASS: $19 held, fixed (0 failed rules, no visible change, 33 s), then $19 charged; fixed file downloaded |
| B. scanned 1-page document | PASS: checked; no checkout created; nothing held |
| C. buyer leaves Stripe's page | PASS: checkout left open and unpaid; no payment; nothing held |

## What had to change first (each on the owner's yes)

1. The Stripe test secret key added to the preview settings (Doppler `stg`).
2. A separate test engine (a Railway `staging` copy of the engine) running
   this branch's code against the practice database. The single engine had
   been reading the live database while the preview, since Nov 11, stores
   uploads in the practice database, so every preview check failed with
   "object not readable". The copy also needed the engine's build file named
   explicitly; without it the platform built the website instead.
3. The preview's site address set to its own domain. It had been the live
   platform address, so Stripe returned the buyer to the live site, where the
   report did not exist and the fix never started.

## Findings

- The first test A attempt left one $19 test hold uncaptured. Nothing
  captures it; Stripe drops it after about seven days.
- The stale-hold sweep runs only on production, against the live database,
  so it never releases a preview hold.
- The homepage sample card (a real past check of the same agenda) stays on
  screen when a check fails and carries no "sample" label, so it can read as
  the buyer's own score. Proposed fix awaits the owner.
- The test-mode webhook for the new Stripe account is not set up yet (owner,
  before launch). The handler only logs, so payments are not affected.
- verify.sh: 9 of 12 pass; 3 cannot run because the deployed site is behind
  the platform sign-in.
