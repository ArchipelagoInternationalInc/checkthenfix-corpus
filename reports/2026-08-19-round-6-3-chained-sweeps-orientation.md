# Round 6.3 — chained sweeps, orientation paragraph shipped

**Date:** 2026-08-19 · **Branch:** `design-pass` · 296 tests, lint, typecheck and production build clean.

---

## The finding: the dead period was never the rest constant

The grid renders 14 rows. The lattice is masked to fade out over the bottom of
the hero and clipped by `overflow: hidden`.

> Measured at 1280×676: the hero is **676px** tall and a cell is **80px**, so
> **rows 8–13 sit entirely below the hero** and never render.

Every sweep was striking **96 invisible cells — 96 × 135ms = 12.96 seconds** per
sweep spent on motion nobody can see.

**Round 6.2's instrumentation was structurally blind to this.** It counted
`animationstart` events, and an event fires whether or not a mask lets the pixel
through. It reported the motion as perfectly healthy while a third of each minute
was invisible. That is the lesson worth keeping: I measured the event stream, and
the owner was watching pixels.

It cannot be a constant, either:

| Viewport | Hero | Cell | Visible rows | Visible cells |
|---|---|---|---|---|
| 1280 | 676px | 80px | **8 of 14** | 128 of 224 |
| 390 | 1203px | 24.4px | **14 of 14** | 224 of 224 |

So the visible field is **measured on mount** from the grid's own box, and cells
beyond it never animate — a second way for a cell to rest, alongside being before
the origin.

---

## Chained sweeps

`REST_MS` **400 → 120**. Origins now range over the whole visible coloured field
(columns 4–11, past the 22% edge fades) rather than the old 16-cell H1 zone, with
a floor on sweep length so an origin near the end cannot produce a one-cell flash.

### The origin rule needed strengthening

The first rule was Chebyshev ≥ 3. Three captured sweeps came out at cells **36,
39 and 43 — all in row 2**, three and four columns apart. Every pair passed the
distance test and the sequence still read as starting in the same place three
times, because the coloured band is only eight columns wide and a horizontal
shift inside one row is not "somewhere else".

A **minimum row shift of 2** is now required as well.

### Sixty-second watch, shipped configuration

| Measure | Result | Limit |
|---|---|---|
| **Max dead period** | **0.282s** | ~1s |
| Sweeps in 60s | 6 | — |
| Distinct origins | **6 of 6** (rows 2/6/1/4/1/6) | — |
| Consecutive origins too close | **0** | 0 |
| Strikes outside the visible field | **0** | 0 |
| **Out-of-sequence strikes** | **0** | 0 |

"Dead" is defined as the gap **after a fade completes**: a cell struck at T is
still visibly fading until T + 1.135s, so the screen is only still in the tail.

**Decay was not touched and no overlap trimming was needed.** With rest at 120ms
the previous sweep's last fade finishes before the next begins, so the overlap the
ruling allowed for did not arise.

### A measurement artifact, corrected

The ordering check counts **complete sweeps only**. The watch stops on a wall
clock, so the last sweep is cut off mid-flight; comparing it against its full
expected length counted every unstruck cell as a violation. The first run reported
**52** that way — all of them the truncated tail, none of them real.

Frames: `design/screenshots/2026-08-19-r6-3-origins/` — origins at rows 2, 4, 6.

---

## Orientation copy

### Shipped

The hero paragraph, from the bank, replacing "Upload any PDF…".

**One sentence is not final.** The ruling wrote it as *"This applies to [agendas,
forms, newsletters, and many other documents — pending owner's grammar call, PM
will confirm]"*. The candidate wording is used; the bracket is a note, not copy,
and the em dash inside it belongs to the note.

Captures: `design/screenshots/2026-08-19-r6-3-orientation-live/`.

### Held — the one item not shipped

The ruling asks for the band *"verbatim as staged (The rule / The problem / The
tool)"*. What was staged carried entirely different labels: **"Structure, not
appearance", "Scanned pages are pictures", "What your report says"**.

There is no version of the band that is both verbatim and those three labels.
Shipping it either way publishes something nobody approved — approved labels over
bodies written for other headings, or bodies under headings the owner did not
choose.

So the band stays behind its flag with the three named labels and bodies rewritten
to match them, drawing only on facts already in approved copy, plus the
`/deadline` link on "The rule".

**Note for the owner:** "The rule" currently repeats the deadline sentence already
shown in the callout above it on the same page.

Captures: `design/screenshots/2026-08-19-r6-3-band-staged/`.

### Guards

| Target | Claim violations | Em/en dashes |
|---|---|---|
| Shipped homepage | **0** | none |
| 9 staged orientation strings | **0** | none |

---

## Corpus basis for "most legacy documents fail compliance"

| Measure | Result |
|---|---|
| Documents in `corpus/INTAKE.tsv` | 32 |
| Pass PDF/UA under veraPDF | **0 of 32** |
| Untagged | 19 of 32 |

**Convenience sample** collected for evaluation, not a random sample of
public-sector documents. It supports the sentence descriptively and is **not a
population estimate**.

---

## Decisions for the owner

1. **The grammar call** on "This applies to agendas, forms, newsletters, and many
   other documents." — shipped as the candidate wording.
2. **Band bodies** under The rule / The problem / The tool — rewritten and staged
   because "verbatim as staged" named labels that did not exist. Approve or
   replace, then the flag comes off.
3. **"The rule" duplicates the deadline callout** already on the page. Cut one.
4. **Sweeps starting in the lower rows open faint** — the bottom mask puts row 6
   at 0.48 alpha and row 7 at 0.23, by the ratified design. Accept, or restrict
   origins to the upper rows.
