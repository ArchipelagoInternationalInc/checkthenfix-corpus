# Round 8: seven-review adjudication applied, and two unsubstantiated claims taken off production

**Date:** 2026-08-24 · Deployed and verified live 01:39:16Z.
**Gate review:** `/dev/gate-review` on the production host — HTTP 200, noindex,
robots-disallowed, sections 2 to 4 rendering with the counsel-held labels.

## The finding that mattered most

**Two claims were serving on production that could not be substantiated.**

The FAQ stated that **every report includes a reading-order transcript**.
Nothing in the codebase produces one. The same answer promised a feedback
channel "answered fast" — an operations commitment nobody has staffed — and said
the product "proves the technical results", when a validator reports rather than
proves.

All three are gone. The transcript claim is on the held-claims register and
cannot return until a transcript exists and the evidence test can assert it.

This is the class of representation the FTC substantiation standard and the
accessiBe order are about, and it was found by working the verification register
rather than by reading the page.

## Image handling — the top-priority verification

**The engine's behaviour differs from the proposed string, so the string did not
ship.**

What the engine does: the model drafts a description for each image with a
self-reported confidence. A draft is flagged for human review when it cannot be
described responsibly, when its text contains a hedging term, or when confidence
falls below threshold. **Otherwise it is written into the document unflagged.**

The proposed sentence — "Image descriptions are drafted and flagged for your
review" — says drafts are flagged. Most are not. It would tell a buyer that a
human sees every description before a reader does, which is a stronger safety
claim than the product delivers, in the exact direction that gets accessibility
vendors into trouble.

**This is not hypothetical.** The decision record contains a municipal seal
drafted as "a stylized lizard or gecko design … likely an organizational logo"
at confidence 0.72 — above threshold, therefore unflagged, and wrong. A human
audit caught it. Hedge-term detection was added afterwards and would catch that
specific draft today, but hedging is textual evidence, not a guarantee.

Per the review's own first option, the term was **deleted** rather than reworded.
Nothing about image descriptions is claimed on any shipped surface.

## The third card is held, and the layout follows

"Every decision made, with the items that need your review listed first" was
VERIFY-gated on test evidence across all report types. **There is no remediation
report builder at all** — "listed first" survives only as a comment describing an
intention.

The band ships **two cards**, and the grid declares the count rather than leaving
an empty third cell that reads as a loading failure. The decline line is held for
the same class of reason: it promised detection before payment and no charge, and
there is no billing path for either to attach to.

## Guards (§E), mutation-tested

**12 new rules** — self-praise adjectives on the product's own actions,
prove/proves, roadmap forms, vendor-deck vocabulary.

**Plus absolutes, which needed a different mechanism.** "Every / all / any /
always" attached to product behaviour is banned *unless CI-tested true*, and that
exception is the whole rule: a flat ban would delete "Every report lists the
automated checks that ran", which is true and which the product needs to say.

So the exception is keyed to evidence rather than judgement. An absolute passes
only if it appears in an evidence map naming a test file **that exists on disk**.
An entry cannot be added by asserting the claim is fine — someone has to write
the test, and deleting the test breaks the build rather than silently
re-permitting the claim. Tested in both directions, including the case where the
entry exists and the file does not.

Limitation statements are exempt, per the review: "or any other law or standard"
is a disclaimer, not an absolute.

## Evidence written rather than asserted

| Item | Status |
|---|---|
| Score formula | **Documented and tested.** Truncation, not rounding: 9/21 reports 42, not 43. Direction is deliberate — truncation can only understate |
| Report contents | **Tested** across types including the free check: validator named with version, every check listed, framing statement present, group totals sum to the whole set |
| Timing | **Substantiated** from recorded corpus measurements: **32 of 32 under 60s**, median 2.80s, 90th percentile 5.42s, slowest 28.41s — on a 507-page budget book |
| Citations | **Re-run.** V1 discharged: 89 FR 31320 confirmed; 91 FR 20902 confirmed |

A substantiation record now holds all of it, including a **NOT SUBSTANTIATED**
table naming every claim held out of the product and exactly what would
discharge each.

## Three tests were pinned to retired copy

They failed on copy that had been deliberately improved. One asserted the FAQ
contained the word "guaranteeing" — from a sentence the adjudication removed as a
defensive swipe at competitors. Another parsed the schema block with a regex
sensitive to quote style. A third asserted the old score label.

Each was rewritten to test the property rather than the wording: that the page
can still say "ADA compliant" while denying it. A test pinned to one phrasing
turns every future approved edit into a false failure.

## Also applied

Eyebrow precision, hero paragraph, sub-headline, card 2 and card 3 titles and
bodies, section-header contraction, footer disclaimer, AUTOMATED CHECK SCORE
label across all four score surfaces, explainer E1 to E7, all FAQ finals,
samples bugs B1 and B2, checklist rewrite, state-template correction.

Owner decisions applied: the forever pledge cut; card 3 retitled; both FREE
instances kept.

## Guard counts

| | |
|---|---|
| Files scanned | 32 |
| Bytes of copy scanned | 118,623 |
| Claims rules / violations | 8 / **0** |
| House-style rules / violations | 28 / **0** |
| Absolutes violations | **0** |
| Dated-claims violations | **0** |
| Label-punctuation violations | **0** |

409 tests, lint and typecheck clean, zero console errors, no horizontal overflow
at either width.

## Verified on the deployed URL

Adjudicated strings present; retired and held strings absent, including the
forever pledge, the old card titles, the no-charge line, and "Every decision
made". Samples bugs confirmed fixed in the rendered DOM: "38% → 95%" and
"1 page" / "12 pages", no "page(s)".

## Register status — open items

Every one of these is held OUT of the product, not pending on a page.

| Claim | Gate |
|---|---|
| Reading-order transcript | Build it, then assert it in the evidence test |
| "Every decision made" + review-items-first sort | Build the remediation report, test the sort |
| Scope detection before payment | Billing path |
| "You are not charged" | Billing path that enforces it, plus owner approval |
| Image descriptions sentence | Flag all drafts, or reword to match the tiering |
| Refund and visual-preservation terms | Attorney |
| Encryption, 72-hour deletion, model-training | Behaviour verified, then attorney |
| Feedback channel and response time | Operations can meet it every time |
| Privacy / Terms / Refund links in footer | Build the pages |
| Per-sample proof files | Source URL, retrieval date, both PDFs, validator output, permission basis |

## One note for the owner

The required framing statement carried on every report still reads "Automated
checks **verify** technical structure … accessibility compliance is
**ultimately** your organization's legal responsibility" — the two words the
footer edit removed for good reasons. It was left alone because it is a
compliance-mandated string governed by its own document, and changing it was not
in this round's instructions. It is raised rather than edited.
