# Copy set v2: section 1 ships, sections 2 to 4 quarantined, house-style guard added

**Date:** 2026-08-20

## What shipped

Section 1 of the merged copy set, owner-approved, verbatim:

| Element | Change |
|---|---|
| Eyebrow | Replaced. Now a statement of who the product is for, one line |
| H1 | Replaced. "Find every failure" is gone and is now banned outright |
| Hero paragraph | Replaced. Narrower and truer about the rule's reach |
| Proof strip | All three bodies replaced; label 1 retitled |
| "Then fix it." | **New section**, follows How it works |
| Orientation band, The Rule | Now "state and local governments' web content, including many PDFs" |

**The "Then fix it." status line did not ship.** The copy set offers two options
and says the owner picks at publication. Shipping either would be choosing on
the owner's behalf about when a thing can be bought. Both candidates are parked
in the quarantined module.

**Score context line, one constant, four surfaces:** report page, homepage
sample card, report PDF, and text report. One source rather than four copies,
because a disclaimer living in four places is a disclaimer that will eventually
be corrected in three.

## What did not ship

Sections 2 to 4 replaced their earlier drafts in the quarantined module and
remain there for the owner's sitting. The previous explainer draft described tag
structure as "the layer underneath", which section 5 now bans as metaphor; the
replacement says what the thing is instead.

### The [COUNSEL] hold is a field, not a comment

Two FAQ answers describe undertakings to a customer rather than the product, and
publish only after attorney review. They are marked with a `hold` **field**,
because a comment cannot be enforced. A helper reads the held sentences from
that data and a guard hunts them across every publish path, so adding a third
held answer is covered automatically rather than requiring someone to remember.

The gate route labels them "Held for attorney review. Does not publish on
approval." A draft and a thing an attorney must clear are different states, and
the reader is being asked to act on one of them.

## Section 5 guard

New module, **16 rules across three classes**: unqualified performance
absolutes, AI-tell phrases, and "verified" where the honest word is "validated".

Kept **separate from the claims guard** deliberately. That one encodes legal
exposure; this one encodes credibility. Loosening one must never quietly loosen
the other.

**No denial-proximity escape here**, unlike the claims guard, and the asymmetry
is the point: the product must be able to say "no tool can make you ADA
compliant", so that guard needs a use/mention exit. Nothing needs to say
"seamlessly" in order to disown it, so an exit here would only be a way through.

### The CSS carve-out

"100%" is both a banned absolute and the most common value in CSS. A guard that
fires on a width declaration is a guard someone deletes within a week, which is
worse than no guard, because its absence is then mistaken for approval. CSS
declarations are stripped before matching.

Tested in **both directions**: the style value is ignored, and a real claim in
the same file still fires. Asserting only the first half would also pass if the
rule had been deleted.

### Mutation-tested

16 plants, one per rule, plus a coverage assertion so adding a rule without a
plant fails rather than passing quietly. Also tested: case variance, banned text
written as HTML entities, and that the failure message points at the offending
words rather than a nearby fragment.

## Guard counts, full surface

| | |
|---|---|
| Files scanned | 32 |
| Bytes of copy scanned | 115,810 |
| Claims rules / violations | 8 / **0** |
| House-style rules / violations | 16 / **0** |
| Dated-claims violations | **0** |

**One violation was found and fixed en route.** The gate-review colour swatch
still rendered the retired proof-strip label as its sample text. It is a
review-only route, not a product surface, but a banned phrase sitting in a
preview is how it gets copied back into shipping copy.

## Citations verified, not asserted

The interim final rule entry had carried an agency-index URL as an honest
placeholder rather than a guessed document number. Resolved against the Federal
Register API: **document 2026-07663, published 2026-04-20, action "Interim final
rule; request for comments", citation 91 FR 20902**, Justice Department. The
2024 final rule is confirmed as 2024-07758, 89 FR 31320.

**One near miss worth recording.** The same search returns 2026-09266, an
extension of compliance dates with an almost identical title published three
weeks later. That one amends Section 504 obligations for recipients of
departmental financial assistance, **not ADA Title II**. Citing it on a deadline
page would have been wrong in a way no reader could catch.

A script now re-proves all six links and both document numbers rather than
asking anyone to trust them. One source host answers every scripted request with
a bot challenge, so it gets a third state, CHALLENGED, distinct from both pass
and fail: calling it a failure trains the reader to ignore the output, and
calling it a pass claims a verification that did not happen.

Two citation swaps that section 2 requires are staged rather than applied. The
live page is legal-information copy, and swapping its citations ahead of the
gate would ship part of an unapproved section. Both replacements were fetched
and are ready; they move when section 2 is approved.

## Rendering, verified at both widths

Desktop and 390px mobile, reduced-motion, **zero console errors, no horizontal
overflow at either width**. Confirmed programmatically at both widths: the score
context line renders, the new section renders, the status line is absent, the
retitled proof label is correct, and no counsel-held sentence appears in the DOM.

### H1 line count: measured, raised, not quietly fixed

The approved H1 is 63 characters where the previous one was 43, giving **five
lines in a 496px column** at desktop.

Two wrap strategies were tried and **both measured as exact no-ops**: one is
capped at four lines in this engine, and the other targets single-word orphans
while the last line is two words. Height was identical in all three states. The
weaker one is kept only because it costs nothing if the copy later ends on a
single word; it did not improve this heading, and the comment in the stylesheet
says so.

**This is an owner decision about type size, not a Builder fix.** Shrinking a
size the owner already set, to accommodate copy the owner already approved,
would be resolving a design question by silently overriding one of the two
inputs.

## Checks

370 tests passing, lint and typecheck clean.

## Open for the owner

- The "Then fix it." status line: two candidates, parked.
- The five-line H1 at desktop: type size or accept.
- Sections 2 to 4: the sitting.
- The two citation swaps, staged and ready.
- Pricing placeholder in the FAQ, and the two counsel-held answers.
