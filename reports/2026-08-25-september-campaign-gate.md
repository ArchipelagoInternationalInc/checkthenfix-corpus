# September campaign — gate report

**Date:** 2026-08-25 · **Staged, not deployed.** Nothing new is publicly
reachable. No charge exists anywhere in the system.

---

## The blocker, first

**Slice A cannot be completed as written.** There is no Stripe secret in any
Doppler config and no Stripe dependency in the project. The test-mode
walkthrough — purchase, subscribe, decline-uncharged, refund, receipt, with
screenshots — needs credentials, and asking for a key to be pasted into a
transcript is against the standing rule.

**What that blocks:** Stripe products, checkout, webhooks, receipts, the refund
path, page metering, and magic-link accounts.

**To unblock:** `STRIPE_SECRET_KEY`, `STRIPE_PUBLISHABLE_KEY` and
`STRIPE_WEBHOOK_SECRET` (test mode) in Doppler, clipboard to Doppler, never
through a transcript.

## What was built instead — and it is the part the brief singles out

The brief asks for the decline path to make "you are not charged" true **by
construction**, and for the test proving it. That needs no key, and it is done.

**The ordering is the guarantee.** A card is authorised before work begins and
captured only after the document clears scope detection. Authorisation places a
hold; it moves no money.

```
authorise → scope detection → clears?  → capture   (money moves)
                            → declines? → cancel   (hold released)
```

There is no path from a declined document to a capture, because capture is
reachable only through the `clears` branch.

**The proof: 10 assertions, all passing.**

| Assertion | Why it is there |
|---|---|
| Decline cancels, never captures | The stated claim |
| Scope detection **throwing** also cancels | The case that would quietly charge if the code failed open |
| Pre-check decline never even authorises | No hold placed at all |
| **In-scope DOES capture** | Positive control — without it the suite would pass if capture were simply deleted |
| Capture never precedes scope detection | The guarantee is ordering, not remembering to skip |

**This clears the engineering gate on that held claim.** What remains before the
sentence can ship is the billing path itself.

One pricing decision worth surfacing: over 100 pages **quotes** rather than
charging the top tier. The cost table shows those documents cost materially more
to process, and charging $119 for a 507-page budget book is the quiet overreach
a price list exists to prevent.

## Slice F — counsel evidence bundle, and the gap it found

Written attorney-readable. Every claim points at a file, a test, or a measured
response.

**It found something.** "No document contents in logs" has been a standing rule
since the first session, held up by comments. Counsel asks what *enforces* it,
and "several files say so in prose" is not an answer. It is now a test that
fails the build if any content-bearing value — extracted text, page images, file
buffers, model prompts, filenames — reaches a logging call. Tested in both
directions, so it cannot be satisfied by logging nothing at all.

Other contents: transport security **measured** (HSTS two years with preload;
`http` returns 308 to `https`); the private storage bucket shown as a migration
rather than a dashboard click; the 72-hour deletion job with its authentication
that **fails closed** when unconfigured; and the AI provider's training clause
quoted verbatim with its effective date.

**Encryption at rest is described as what it is** — a vendor property we did not
implement and cannot demonstrate from our own code — rather than borrowed as if
it were our control.

The bundle ends with a table of what is **not** evidenced: backups,
sub-processors, operator access, data residency. An evidence bundle containing
only what went well is not evidence.

## Slices B, C, D — staged for the single reading session

All copy is in the staging file, guards clean, nothing reachable.

- **Purchasing page** — vendor block (two fields need the owner), W-9 on
  request, and a security summary where **every sentence maps to an item in the
  evidence bundle**. Backups, sub-processors and residency are *absent* and
  named as absent, because a purchasing page is exactly where a comfortable
  guess does the most damage.
- **Launch artifact** — the survey, with the honest paragraph naming its own
  denominator: 27 documents from 17 of 50 attempted municipalities is **not** a
  survey of North Carolina, and the artifact says so rather than rounding it
  into "70% of city documents fail". Every number traces to the run record. No
  entity named.
- **Press note** — the finding is the story; the tool is how it was obtained.
- **Four newsletters** — welcome, what the extension actually changed, what to
  fix first, the checklist.

**Not done:** Brevo campaign templates were not created, and the double-opt-in
end-to-end test was not run. Both touch a live external service, and the brief's
own rule is that nothing sends.

## Slice E — research only

**No outreach. No email, no form, no contact of any kind.**

Five categories compiled from public directories. Two individual staff email
addresses appeared in the sources and are **deliberately not listed** — an
address a person published so members could send them artwork is not an outreach
channel, and using it as one is the behaviour that gets a sender remembered for
the wrong reason.

**Category 4 (government web professionals) is recorded as unresolved.** The
commonly cited association domain did not resolve, and the nearest alternative
is a federal community of practice — a place to learn from, not to pitch. Left
unresolved rather than padded.

## Slice G — sweep

| | |
|---|---|
| Favicon | Derived from the wordmark's **own "c" glyph** — the owner's artwork, not new drawing |
| Social preview | 1200×630, composed from the same artwork, static file |
| Custom 404 | Links generate from `NAV_LINKS`, so it cannot advertise a page the header stopped advertising |
| Crawl, 6 pages × 2 widths | **0 dead links · 0 console errors · no overflow · exactly one h1 each** |

**Not wired: analytics and error monitoring.** The studio standard is not stated
in any document available here; error monitoring needs a DSN that does not
exist; and analytics sends visitor data off-site, which is not a call to make
silently on a product whose promise is careful handling.

## Delegation log

| Call | Decision | Basis |
|---|---|---|
| Stripe | Stopped at the credential boundary; built everything that does not need a key | Standing rule against secrets in transcripts |
| Over-100-page pricing | Quote, not top tier | Cost table shows materially higher cost |
| Staff emails found in directories | Excluded from the research doc | Published for a different purpose |
| Analytics / monitoring | Not wired, flagged | Standard not found; one needs a DSN; the other sends data off-site |
| Brevo templates | Not created | Touches a live service; brief says nothing sends |
| Deploy | Not deployed | Campaign is staged for one gate |

435 tests, lint and typecheck clean.
