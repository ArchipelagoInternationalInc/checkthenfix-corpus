# Design round 3 — hero lattice, glass surface, and a palette proposal

**Session date:** 2026-08-18 · **Branch:** `design-pass` · **Commit:** `ba36965`

---

## ⚠️ Awaiting sign-off before it goes further

The palette below is **proposed, not applied**. The report page has not been
repainted, per instruction. Two measurements shaped the proposal, and both
change what a straight colour swap can be.

### Measurement 1 — the reference teal cannot carry text

The governing aesthetic reference uses a bright mint-teal for buttons and large
fills. Measured against white it is **2.17 : 1**. The AA threshold for text is
4.5 : 1. A single-token swap to "the reference teal" would break the hard
contrast constraint the moment it shipped.

So the accent must be **two-tone**: one value dark enough to carry text, one
bright enough to look like the reference on large fills.

| Token | Proposed | On paper | White on it | Role |
| --- | --- | --- | --- | --- |
| `accent` | `#0E7490` | 5.13 : 1 | 5.36 : 1 | text, links, buttons — AA both directions |
| `accentBright` | `#2EC4B6` | 2.07 : 1 | — | **large fills only, never text** |
| `accentWash` | `#E6F6F2` | — | — | grounds and plates |
| `fail` | `#C2334D` | 5.19 : 1 | 5.42 : 1 | modern rose-red, replacing `#B3372B` |
| `pass` | `#1E7F4F` | 4.78 : 1 | 5.00 : 1 | unchanged — already passes and stays distinct |

Retired: `#1F5FA8` (accent) and `#B3372B` (fail).

### Measurement 2 — pass and fail collapse for some readers

Simulated under deuteranopia, the pass and fail hues become
**`#636351` and `#5D5D4A`** — indistinguishable.

This is **equally true of the current palette**, so the proposal is not a
regression. It is reported because it settles an argument: "status is never
conveyed by colour alone" is load-bearing, not belt-and-braces. Every status in
the product must keep its icon and its word, and the guard tests that enforce
that are protecting real readers rather than a style rule.

One thing for the owner to weigh: a teal accent sits **42.6°** from the pass
green in hue, where the retiring blue sat further away. Still tellable, but the
accent is now a closer neighbour to "pass" than it used to be.

---

## What was built

**Hero motif — uniform tangent circle lattice.** Circles of one size, touching
on all four sides, filling the hero, with an animated gradient beneath
travelling white → green → cyan → blue → white in hue and value. The lattice
holds still and the ground moves: a grid that moves is a grid that jitters.

The first attempt produced ellipses covering only the top third. The cause is
worth recording because it is not obvious: a percentage row height in CSS grid
resolves against the grid's **height**, while columns resolve against its
**width**, so on any non-square hero the cells stretch. Square cells by
aspect-ratio make tangency true by construction at any viewport size.

**The check sweep.** Row by row, left to right; each circle's check appears
suddenly, brightens with its circle, then fades back. Delay is computed per cell
from its row and column, so the wave is a property of position rather than a
keyframe written per circle. The rise is short and the fall long — that
asymmetry is what makes it read as a check landing rather than a pulse.

No animation library was added. Every cell runs one fixed-length loop whose only
variable is its start delay, which is precisely what `animation-delay` is for.

**Glass surface language, marketing only.** Translucent white, backdrop blur,
generous radii, soft lift. Implemented as an opt-in class rather than a
redefinition of the existing card, so the marketing and app surfaces cannot
drift into each other. Falls back to opaque where backdrop blur is unsupported,
because a translucent smear over a moving lattice is worse than a plain card.
Large marketing numerals move to a light weight; the report page keeps its
weight, where a number is a finding rather than a flourish.

---

## Counts

| Measure | Value |
| --- | --- |
| Tests | 196, all passing |
| Lint / typecheck | clean |
| Screenshots | 2 page captures + 6 motion frames + 1 reduced-motion frame |
| Animation libraries added | 0 |

Reduced-motion is **verified, not asserted**: the capture script reloads under an
emulated reduce preference and fails if any animation is still running. It
reported 0.

---

## Notes for the record

- The governing aesthetic references were supplied as screenshots inside a
  conversation. Those cannot be written to disk from there, so they are recorded
  by title, author and URL instead — more durable than a copied file, and it
  avoids committing a third party's portfolio work into a repository.
- The owner's own runnable prototype **was** available on disk and is now
  committed as a comp. Its tonal ramp was read out of its stylesheet rather than
  eyeballed, which is where the gradient's colour stops come from.
- That prototype's own note reads "Composition, not dots: few large overlapping
  shapes" — the opposite of the uniform lattice the brief specifies. The brief
  is the later instruction and wins; flagging it in case the prototype was meant
  to govern instead.

---

## Next

Slice 4 — supporting states: staged progress, decline screens, the
big-document email screen, and the report PDF template. The palette repaint
waits on sign-off.
