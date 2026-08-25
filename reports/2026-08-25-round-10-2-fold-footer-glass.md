# Round 10.2: fold discipline, dark footer, glass under the proof strip, air

**Date:** 2026-08-25 · **STAGED, not deployed** — the brief holds deploy for
owner review. Production still serves round 10.1.

## Housekeeping first: the cost table exists

**Yes, it was produced** — round 9.1, committed as `MODEL_COST_TABLE.md` and
`.json`. It does not need to ride along. Restated so the pricing sitting has it
in one place:

| Document class | n | Median | Max |
|---|---|---|---|
| Agenda / minutes, text only (1-3pp) | 2 | **$0.0131** | $0.0131 |
| Agenda / minutes with images (1-3pp) | 15 | **$0.0245** | $0.0703 |
| Agenda package (4-20pp) | 7 | **$0.0600** | $0.2343 |
| Long report / budget book (20pp+) | 8 | **$0.1628** | $0.1949 |
| **Fleet** | **32** | **$0.0550** | **$0.2343** |

Judgment passes only. The standing caveat holds: output tokens are calibrated
against two documents, and running the judge now that usage is recorded replaces
that estimate with measurement.

## 1. The hero fits one view

Panel height **798px → 667px**.

| Monitor | Usable viewport | Result |
|---|---|---|
| 1920×1080 | 980px | **FITS**, +216px clearance |
| 1440×900 | 800px | **FITS**, +36px clearance (was *overflows by 119*) |

Everything the acceptance names is above the fold: nav, eyebrow, H1, subhead,
paragraph, and the whole dark panel including the sample card down to its
validator line.

Taken from three places. The upload zone was taller than its job — two short
lines and a glyph — so its padding went 2.5rem → 1.25rem with a smaller icon.
The sample card's padding, gaps and score numeral each came down one step.
The hero's top padding went 3.5rem → 2rem, which matters more than it sounds:
the panel's top edge is nav plus that padding, so every 8px there is 8px off its
bottom.

**Nothing was removed from the card.** Filename, score, all three counts and the
validator line still render. The 62/38 split is untouched. 390px is not a fold
target and still scrolls, as a phone should.

## 2. The footer joins the dark world

A near-black navy gradient, continuous with the CTA band: the measured gap
between them is **0px** — no border, no margin, no light seam. The disclaimer at
75% white measures **10.57:1** at the note's start and **10.75:1** at its end.
Quiet weight, not quiet contrast.

**The dark treatment is opt-in, and the direction is the point.** This footer
lives in the *root* layout, so it also renders on the report surface — which is
flat paper by standing ruling. A new marketing route-group layout marks its own
subtree and the CSS matches on that.

Marking marketing rather than excluding the app surface means a browser without
`:has()` keeps every footer light. The failure mode is a marketing page missing
a refinement, not a product surface gaining one it was never meant to have.

## 3. Glass under the proof strip

Each of the three items is now its own glass panel, same translucency, radius
and contrast rules as every other glass card.

Round 9 stripped that container deliberately, so four card rows would stop
reading as one repeated shape. That was right when the ground beneath was
near-white. At full bloom strength the same decision left three headings and
their sentences sitting on raw colour.

Measured effect: the proof strip's worst contrast margin **+0.90 → +2.34**.

## 4. Air above the how-it-works heading

**2.5rem → 6rem**, tuned by eye at 4, 5 and 6. Below 6 the heading still read as
a caption on the colour band above it rather than the start of a new section.

## Measurements after the changes

Bloom floors were re-run once the glass was in, and **improved**: proof strip
ΔE2000 **19.5 → 25.2**, New to this **27.7**. The glass sits over the bloom; it
does not dim it.

Contrast: **47 rendered text nodes, 0 failures.** Analytic matrix: **16 pairs, 0
failures**. Reduced motion: **0 running animations**. No horizontal overflow at
any width. 421 tests, lint and typecheck clean.

## One thing that is not a defect

Full-page stitched captures show a faint vertical seam near the page centre. A
fresh viewport screenshot of that exact region measures a maximum deviation of
**1.6/255** — there is no line in the product, only in `captureBeyondViewport`
output. Recorded so it is not chased again from a screenshot.

## Awaiting the owner

Review, then deploy. Copy and the hero animation are untouched.
