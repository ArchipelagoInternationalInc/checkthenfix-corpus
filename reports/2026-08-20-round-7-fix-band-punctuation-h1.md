# Round 7: fix section rebuilt as a band, label punctuation rule, H1 down a step, deployed

**Date:** 2026-08-20 · Deployed and verified live at 21:19:56Z.

## Deploy state — GREEN

The brief flagged as priority that production still served the retired
headline. **It did not.** That copy went live in the previous round and the
public alias was already serving v2 when this round opened: the retired headline
returned zero occurrences, and the review route was already reachable. The
premise was one round stale. Round 7's own changes are now deployed on top.

Standard verification against the live URL: **six of six, TRIPLE GREEN.**

Confirmed in the deployed DOM at both widths:

| | |
|---|---|
| Condensed labels rendered | 13 |
| Labels ending in a period | **0** |
| H1, desktop | 48px, **4 lines** |
| H1, 390px | 36.03px, **4 lines** |
| Horizontal overflow | none at either width |
| Review route | HTTP 200, noindex present, robots still disallows the tree |

The review route serves sections 2 to 4 with the counsel-held labels rendering:
"Held for attorney review. Does not publish on approval." and the pricing
placeholder's "Awaiting your prices."

**A verification of mine was briefly wrong and is worth recording.** The first
production check appeared to show three labels still carrying periods. They did
not: the search patterns ended in a full stop, which is a regex wildcard, so
"Upload Your PDF." was matching "Upload Your PDF<". Re-run as fixed strings and
confirmed against the rendered DOM, all thirteen labels are clean. A checking
tool that reports a false positive costs the same trust as one that misses a
real fault.

## 1. The Fix, Explained

Rebuilt from a prose column into a full-width band: a wash plate holding three
raised cards, the same treatment as the how-it-works trio, heading at 36px
matching every other section heading.

The prose version put two long paragraphs in a 62-character measure inside a
full-width plate, which left half the plate empty at desktop and read as an
unfinished section rather than a deliberate one.

**The three cards carry no number badge**, unlike the how-it-works trio. Those
three are a sequence performed in order; these three are a set returned
together. The one graphic element that would assert an order is the one left
out.

The decline sentence sits below the cards rather than in one: it is the
condition on all three, and setting it beside them would read as a fourth thing
you receive.

### Copy provenance, because the brief's source did not arrive

The brief said "structure and copy verbatim from the PM section above", and **no
PM section came with the paste.** Rather than write marketing prose to fill the
gap, every sentence in the band is re-lineated from the copy set paragraph that
is already owner-approved and already shipping. The card bodies are that
sentence's own clauses; card one borrows the appearance clause from the sentence
before it, which belongs with the fixed file.

**Three strings are Builder-set and need confirming or replacing:** the card
labels "The Fixed PDF", "Before and After", "An Itemized Report". They are noun
phrases lifted from the same approved sentence rather than newly written lines,
but they are the only text on the page not traceable to approved copy, and they
should not stay unexamined just because they read naturally.

The status line ships now that the owner has picked it.

## 2. Label punctuation, standing rule

**8 strings changed:** six label fields on the homepage, two in the review
route's colour swatch. Thirteen labels now render with no terminal period.
Full-sentence headlines keep their punctuation.

Documented in the project conventions **and enforced by a test**, because six
labels carried periods while three siblings did not, for two rounds, and nobody
noticed until the owner read the page. That is precisely the class of thing a
person cannot be relied on to catch on every new string.

### The first version of the enforcement was wrong

The test needed to tell a label from a headline. The first attempt exempted
"sentences" as anything over six words. **The headline it existed to protect is
five words.** It would have exempted nothing it was meant to and waved through
any seven-word label.

Replaced with the real discriminator, which needs no heuristic at all:
full-sentence headlines are inline markup, not label fields, so they are never
in the scanned set. Recorded in the test file rather than quietly corrected,
because the wrong version looked more careful than the right one.

## 3. H1 scale

**Desktop: 60px to 48px**, one rung down the type scale. Four lines at 204px,
down from five at 318px.

**Mobile had to move too.** It was five lines at 390px, past the four the brief
allows, so the exemption did not apply.

Lowering the clamp *floor* would have done nothing, and this is the part worth
recording: at 390px the **preferred term governs**, and a clamp minimum only
applies when the preferred value falls below it. The floor was never active
there. The intercept is what moves 390px, so that is what changed, giving
36.03px and four lines. The floor was lowered to match for widths below 390px,
where it does take over.

Measured at four widths:

| Width | Size | Lines |
|---|---|---|
| 1440 | 48px | 4 |
| 1024 | 48px | 4 |
| 390 | 36.03px | 4 |
| 360 | 36px | **5** |

360px is five lines with no overflow. **Reported rather than tuned**, because
pushing lower to fix it would shrink the 390px reference the project uses as its
mobile standard, to satisfy a width nobody specified.

## Guard counts

| | |
|---|---|
| Files scanned | 32 |
| Bytes of copy scanned | 117,519 |
| Claims rules / violations | 8 / **0** |
| House-style rules / violations | 16 / **0** |
| Dated-claims violations | **0** |
| Label-punctuation violations | **0** |

374 tests passing, lint and typecheck clean.

## Open for the owner

- **The three card labels**, the only Builder-set strings in the new band.
- 360px H1 at five lines: accept, or a smaller floor at the cost of 390px.
- Sections 2 to 4: the sitting.
- The two staged citation swaps.
- Pricing placeholder and the two counsel-held answers.
