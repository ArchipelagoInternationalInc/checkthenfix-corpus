# Slice A complete: payments in test mode, and the no-charge claim proven live

**Date:** 2026-08-25 · Staged on `design-pass`. Test mode only; no live charge
can be created.

---

## The claim, proven against real Stripe

The held claim was: *if a document falls outside what we can remediate, we say
so, and you are not charged.* It has been unshippable for weeks because a policy
is not an enforcement.

**Run in test mode, 2026-08-25:**

| Step | Object | Result |
|---|---|---|
| Purchase | payment intent | `succeeded` · **1900 of 1900 cents received** · receipt issued |
| **Decline** | payment intent | `canceled` · **amount_received 0 of 1900** |
| Subscribe | subscription | **`active`** · first invoice paid 4900 cents · metered 63/60 |
| Refund | refund | `succeeded` · 1900 cents returned · `charge.refunded true` |

**No charge was created on the decline, so none had to be reversed.** That is
the difference between this and a refund policy.

The mechanism is `capture_method: "manual"`. Authorisation places a hold and
moves no money; capture is reachable only through the branch where scope
detection has cleared the document. There is no code path from a declined
document to a capture.

## Safety, and it earned its keep

The keys arrived in the **`dev`** config rather than `stg`, which the first
check caught before anything was created. Every script refuses a key that is not
`sk_test_`, and the account was confirmed as "CheckThenFix sandbox" before the
first write.

`lib/payments/stripe.ts` **throws on a live key** unless `ALLOW_LIVE_STRIPE=true`
is set explicitly — so going live is a decision someone makes, not a key someone
pastes.

## The webhook secret never passed through output

Stripe returns a signing secret **only at creation**, so recovering one means
replacing the endpoint — which is why that is a separate, deliberate script
rather than part of setup.

The secret went from the API response into `doppler secrets set` over stdin and
nowhere else, into both `dev` and `stg`. It was not printed, not written to a
file, and is not in the repository.

## Two corrections during the run, both mine

**The subscription first came back `incomplete`** because no payment method was
attached. That is a correct Stripe state and a useless walkthrough: it proves a
subscription object can exist, not that a subscriber can subscribe. Fixed by
attaching a test card and making it the default before creating the
subscription — it now shows `active` with a paid invoice.

**The evidence image first captioned a $49 subscription invoice as the $19
document purchase**, because it selected the most recent succeeded intent rather
than the one carrying a `document_id`. An evidence image that mislabels its own
evidence is worse than no image.

## What was built

**Products and prices**, idempotent by `lookup_key` so a re-run cannot create a
second "$19" that drifts from the first: three document tiers
(1900/3900/11900) and two subscription plans (4900/14900 per month).

**Guest checkout** — no account, no Customer object, email for the receipt only.
The route **authorises and cannot capture**: a route that could capture would be
a route that could charge for a declined document. Prices come from the
catalogue, never from the request; a client that could name its own amount could
name zero. Over 100 pages returns a **quote**, not the top tier.

**Webhook route** with signature verification that **fails closed** when the
secret is unset — an unverified webhook endpoint is an open write API.

**Metering reports and never blocks.** A public body that hits its allowance
mid-deadline is the customer we least want to stop, and "fair use" that cuts you
off at the number is a quota with a friendlier name. Pages are counted **after**
acceptance, so a declined document never consumes allowance.

**Refunds are operator-guarded, not self-serve.** The refund *policy* is still
with counsel, and shipping a button that enacts an unapproved policy would be
deciding that policy by implementation.

## Register movement

**E5 — scope detection before payment: CLEARED on the engineering side**, with
both unit evidence (10 assertions, including the scope-detection-throws case and
a positive control) and the live run above.

**The copy gate is not cleared.** The refund policy the sentence sits beside is
with counsel, and the checkout surface is unbuilt. The sentence still does not
ship.

## Doppler: the three `dev` gaps are intentional

Confirmed from the code, not assumed, and now documented in
`docs/DOPPLER_CONFIGS.md`.

| Variable | Why `dev` omits it |
|---|---|
| `NEXT_PUBLIC_SITE_URL` | `siteUrl()` falls back to `localhost:3000`. Setting it would point local development's canonical URLs at the staging host |
| `ENGINE_SERVICE_URL` | The engine client documents it: unset means the pipeline runs **in-process**, which is how local development works |
| `ENGINE_SERVICE_SECRET` | Meaningless without the URL; a half-set pair is treated as unconfigured rather than broken |

**Recommendation: do not fill them.** Each is a routing decision with a
deliberate local fallback, not an oversight.

## Checks

435 tests, lint and typecheck clean.
