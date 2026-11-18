# Refunds page matches the product; launch checklist written (2026-11-02)

Filed on 2026-11-17 from the Studio Mac. The original text, written in a cloud
session that could not push here, was not available on this machine; this
report is rebuilt from that session's handoff notes and says only what they
record.

Nothing was merged or deployed, and nothing was changed in payments, secrets,
the database or DNS.

## What changed

- **Refunds page intro rewritten** (a draft for the owner). It no longer
  promises a preview. It describes the real order of a paid fix: the card is
  held, the document's scope is checked, the fix runs, and the charge is taken
  only after that. The owner's 30-day remedy is unchanged. The checkout step now
  shows the same wording instead of a placeholder.
- **Privacy page** no longer shows a builder placeholder. The checking
  machine's region was left out until it could be confirmed.
- **Deadline paragraph** (staged copy) mentions the April 2026 extension once.
- **New automatic checks on the wording**: no preview promise; no sentence
  tying the charge to the fix in a way the product does not do; no checkout
  placeholder; no builder placeholder on the privacy page; and the privacy page
  must name the AI provider as soon as any deployed code can call it.
- **Launch checklist written**: everything left for the owner, in order, with
  one question and one recommendation each.

## Checks

| Check | Result |
|---|---|
| Identity, lint, types, tests, end-of-session loop | PASS |
| Checks needing secrets, sample documents or the live site | CANNOT-RUN in the cloud session (no access); run later on the Studio Mac |

## Left open then, and where it stands now (Nov 17)

- This report could not be pushed from the cloud. Filed now.
- The checking machine's region: read on Nov 17 (Amsterdam); the owner has
  since approved moving it to US East.
- Whether the end-of-session hook sees the validator path: yes (confirmed
  Nov 17 from the hook's own logs).
- The buying steps did not exist on Nov 2. They were built afterwards and
  tested in Stripe test mode on Nov 12, 14 and 17.
