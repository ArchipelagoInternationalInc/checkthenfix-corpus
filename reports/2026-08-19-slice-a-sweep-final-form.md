# Slice A — sweep final form, checks +20%, nothing white travels

**Date:** 2026-08-19 · **Branch:** `design-pass` · 241 tests, lint and typecheck clean.

Counts, findings and decisions only.

## 1. Forward only — the wrap is deleted

Each cycle picks an origin in the H1-adjacent zone and runs strict reading order
to the last cell of the lattice, then stops.

Round 5.4's wrap kept every cell firing once per cycle. Tidy, and wrong: the
wrapped tail ran along the top-left and finished **beside the headline**, which
put the quietest moment of the gesture in the loudest place. A cycle now always
ends bottom-right.

Cells before the origin carry `animation: none` rather than a delay too large to
matter — a delay would still fire if the loop or the stagger were ever retuned.

| Cycle | Origin | Cells struck | Sweep | Gaps | Fired before origin | Ends at |
|---|---|---|---|---|---|---|
| 1 | 102 (r6, c6) | 121 | 16.335s | `[0.135]` | false | 223, row 13 |
| 2 | 84 (r5, c4) | 140 | 18.765s | `[0.135]` | false | 223, row 13 |

Longest possible sweep is 23.22s from the earliest legal origin, inside the 36s
loop with the 1.135s envelope.

**Accepted cost, stated plainly:** a rest of 11.6s–18.5s before the next origin.
That is the honest consequence of "run to the end and stop" — the alternative is
the wrap just removed. Previously the rest was 5.8s.

Delays now carry three decimals. At two, a 135ms stagger quantises to 10ms and
consecutive gaps alternate 130/140ms — invisible, but the measurement reported
two stagger values where the gesture has one.

## 2. Checks 20% larger

Path and stroke scaled together about the circle centre.

| | Before | After | Factor |
|---|---|---|---|
| Span (of circle diameter) | 19.60% | **23.52%** | ×1.200 |
| Stroke | 3.36% | **4.032%** | ×1.200 |
| Stroke : span ratio | 0.17143 | **0.17143** | unchanged |

Outer extent including the stroke is 27.55% of the diameter.

## 3. Nothing white travels

The ramp began and ended at `#f8fbf9` with `#f2f6fb` near its middle — white in
all but name. `#f8fbf9` measures **0.958** relative luminance against white's
1.0, so a white band slid across the hero with the gradient.

Every stop is now a real tint.

> **Max relative luminance in the moving tile: 0.7492** (`#cfe6da`), down from
> 0.958.

Fading to white now happens in exactly one place: the fixed masks, which do not
move.

### Acceptance

12 frames across the full 44s cycle, moving layer isolated:

- **Zero interior near-white bands.** Every near-white run is welded to the left
  or right edge.
- Planting a single `#f2f6fb` stop back into the tile produces **8 interior
  bands**, including runs 134 and 59 columns wide that visibly cross the hero.
  The test measures what it claims to.

Edge bands' inner boundaries breathe — x=70..120 left, x=1160..1206 right —
reported rather than smoothed away. The overlay is fixed but the tint arriving
under it varies, so the x at which the composite stops reading as white moves
with it. No edge fade over a changing colour can avoid that.

Runs separated by one or two sub-threshold columns are coalesced before
classification: dither at the fade boundary split single pixels off the edge
bands. A travelling stop is tens to hundreds of columns wide, so a 2px tolerance
cannot hide one.

**A false failure worth recording:** the first run of this measurement "failed"
on the white dropzone card and the sample-report card, which sit over the hero
and are white by design. The layer is now lifted above the hero's content before
sampling.

## 4. Headline contrast, re-measured

A darker ramp invalidates the figure recorded when it was pale.

> **Worst H1 contrast: 7.98:1**, at t=14.7s, sampled 12× across the full cycle.

Large text needs 3:1; this clears AAA for normal text as well.

An earlier version of this check sampled 7.5s of the 44s journey and reported
8.62:1. A floor measured over a sixth of the motion is not a floor.

## 5. Delegation exercised

The hero-motion guard asserted a self-closing tag within 120 characters of the
className — a proxy for formatting, not for the rule "the cell has no children".
An ordinary style expression broke it while the cell was still childless. It now
asserts the property directly, and was mutation-tested by giving the cell a
child.

## Captures

`design/screenshots/2026-08-19-design-r5-5-sweep/` — two cycles, different
origins, four frames each.
