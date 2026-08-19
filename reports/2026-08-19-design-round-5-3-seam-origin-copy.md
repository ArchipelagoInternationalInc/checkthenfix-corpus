# Design round 5.3 — seamless loop, H1-anchored sweep, copy addendum

**Session:** 2026-08-19 · **Branch:** `design-pass` · **Commit:** `f7e95f2`

---

## The loop seam was arithmetic, not taste

`background-size: 300%` makes the gradient image three times the container
width. `background-position: p%` does not translate by `p%` — it aligns the
image's `p%` point with the container's `p%` point, which works out as an offset
of `-2W × p/100`.

The animation ran from 0% to 200%, a translation of **-4W**. One tile is **-3W**.
Four is not a whole number of threes, so the frame at loop end was not the frame
at loop start, and the restart was visible. The correct endpoint is **150%**.

The colour ramp already tiled — its first and last stops are the same value — so
nothing about the palette changed.

**Verified as specified:**

| Measure | Result |
| --- | --- |
| Max pixel delta, cycle start vs cycle end | **0 / 255** |
| Pixels differing | **0 of 921,600** |

A reusable script performs the check and parks the lattice at its resting state
first, so the comparison measures the gradient rather than cells that happen to
be mid-decay at those instants.

---

## Sweep origin, and a mistake worth recording

The sweep used to originate at the grid's top-left cell — inside the left-edge
white fade, where white cells sit on white ground. Every cycle's opening strikes
were therefore invisible; the wave only became apparent once it had travelled a
third of the way across.

The origin now sits beside the headline: **31% across the hero**, past the 22%
fade, on coloured ground. The first strike is seen.

**The obvious implementation was wrong, in two measurable ways.** Multiplying
each cell's distance-from-origin by the stagger seemed the natural way to
propagate outward. It broke both properties of the previously ratified blend:

1. The furthest cell is only **13.45 cells** away, so the entire sweep finished
   in **1.8 seconds** and the lattice then sat inert for the remaining 34 seconds
   of the loop.
2. Cells at equal distance fire simultaneously. The ring at distance 4 contains
   **32 cells** — that would have replaced the approved trail of 8–9 with a
   pulsing ring, undoing three rounds of tuning.

The resolution is a one-line distinction worth keeping: **distance sets the
order, the stagger sets the timing.** Ranking cells by distance and assigning
delays by rank preserves the 30-second sweep and the measured concurrency
(**8, 8, 8, 8, 8**) while changing only the spatial order.

Two variants are built for selection, with frames captured for each: a fixed
origin in pure CSS, and an origin reseeded within the headline zone each cycle.
The second one's JavaScript is an interval and a state update; the animation
itself stays declarative.

A guard had to be narrowed rather than deleted along the way. It asserted the
component contained no `useEffect`, as a proxy for "the animation is CSS". The
reseeding variant makes that proxy wrong, so it now forbids the things that would
genuinely mean JavaScript is driving frames, and separately asserts the keyframes
still exist. **A proxy that outlives its accuracy is worse than no guard**, since
it fails for the wrong reason and gets loosened rather than understood.

---

## Copy addendum

Applied verbatim: the framing statement, three FAQ lines, the dropzone
instruction, and two of the three "how it works" steps.

**The framing statement now reads identically in the product and in the
compliance document.** That edit is **flagged for attorney review inside that
document**, per its standing note. The words are unchanged and no claim was
added, narrowed or broadened — only punctuation moved. That is precisely why it
is marked rather than changed quietly: the sentence counsel reviews should never
differ from the sentence that ships without someone knowing.

**Generated strings resolved to colons**, all three:

| Before | After |
| --- | --- |
| `PDF ACCESSIBILITY REPORT — CheckThenFix` (text report) | `PDF ACCESSIBILITY REPORT: CheckThenFix` |
| `PDF ACCESSIBILITY REPORT — CheckThenFix` (PDF header) | `PDF ACCESSIBILITY REPORT: CheckThenFix` |
| `Your accessibility report — ${filename}` (email subject) | `Your accessibility report: ${filename}` |

None of the three changed meaning under a colon, so none were held.

**Dash backlog: 13 → 6.** One entry removed was already stale — the string had
been rewritten in an earlier round and the allowlist had not caught up. That was
checked against source rather than assumed, because an allowlist entry for a
string that no longer exists silently widens what the guard permits.

---

## Held for approval, as instructed

Two strings were not shipped. Proposed wording:

**Score explanation** (currently two dashed clauses on the report page):

> The score is the percentage of **decided** checks, passed or failed, that this
> document passes. Items awaiting human review are not counted either way, so
> they neither help nor hurt it. Computed from the validator's results, not an
> estimate.

**Progress timeout message** (currently one dashed clause):

> We stopped waiting after several minutes. Your document may still be
> processing. The link on this page stays valid, so you can come back to it.

Both replace dashes with full stops and change no meaning. Neither ships until
approved.

Also still on the backlog: the page title, the staged closing CTA, and the
progress "leave this page" line.

---

## Counts

| Measure | Value |
| --- | --- |
| Tests | **222**, all passing |
| Lint / typecheck | clean |
| Loop seam | 0/255, 0 pixels |
| Sweep concurrency | 8 across five samples |
| Dash backlog | **6** (from 13) |
| Animation libraries | 0 |
