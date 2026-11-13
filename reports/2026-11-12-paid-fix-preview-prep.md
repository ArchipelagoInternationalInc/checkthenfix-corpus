# Paid fix on the preview: read-only checks, waiting on the Stripe test key (Nov 12, part 1)

Branch `builder/formal-wording` at 60b1b30. No setting changed, nothing
merged or deployed, no database row, no payment.

## What was checked (names only, no values)

- Doppler `stg` has no `STRIPE_SECRET_KEY`. `STRIPE_WEBHOOK_SECRET` is
  set and is the same value as in `dev` (compared by fingerprint).
- Doppler `dev` holds a Stripe key that is a test key (prefix checked,
  value not printed).
- Doppler `stg` has the AI model key (`JUDGMENT_API_KEY`) and the
  model setting.
- Vercel Preview has no `STRIPE_SECRET_KEY`. That is why the preview
  hides the Fix button and refuses checkout. Every Preview setting shows
  the Doppler sync connection, last updated around Nov 10, so the
  `stg` to Preview copy looks repaired already.
- Checking machine health: OK, veraPDF pinned 1.30, actual 1.30.2.
- The walkthrough `studio-mac-engine-key-and-doppler.md` (Jobs 1 and 2)
  was not found on the Studio Mac or in the shared drives, so it was
  not followed step by step.

## verify.sh

9 of 12 pass (tests 1034 passed; lint and typecheck clean; Stripe
production key is a test key). Checks 8, 9 and 11 cannot run: the
deployed site sits behind the Vercel sign-in, so every page answers
302. Changing protection is outside this task; not worked around.

## Open

- The owner adds the Stripe TEST secret key to Doppler `stg` (agreed in
  the session; the owner pastes it, the Builder checks only that it
  starts `sk_test_`).
- Then one Preview-only redeploy, and the three paid-path walks with
  Stripe's test card: a fixable document, a scanned document, an
  abandoned checkout.
