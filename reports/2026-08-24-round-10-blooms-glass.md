# Round 10: light room, coloured light

**Date:** 2026-08-24 · **NOT DEPLOYED.** Rounds 9, 9.1 and 10 are stacked on
`design-pass` awaiting review. Production still serves round 8.

## What shipped to the branch

A light page where saturated colour blooms upward from the lower part of each
section, with informational content on translucent white glass floating above
it. One bloom per section, saturation low, edges dissolving before the next
section starts. Dark panels carry two hues minimum.

**Copy is untouched. The hero and its lattice are untouched. The report surface
is untouched and guard-tested to stay that way.**

## Contrast, measured twice and two different ways

The brief asked for per-pair measurement against worst-case backdrops. Once
glass sits over a gradient, "the backdrop" stops being one colour, so this was
done analytically **and** against rendered pixels.

**Analytic bound** — every text role against every hue a panel can sit over, at
the bloom ceiling: **16 pairs, 0 failures, smallest margin +1.56.**

**Rendered pixels** — text hidden, page screenshotted, and the ground actually
painted under each text element sampled: **47 nodes, 0 failures.**

| Section | Worst margin |
|---|---|
| Closing CTA | +3.01 |
| How it works | +2.24 |
| Proof strip | +1.82 |
| The Fix | +1.22 |
| Upload panel | +0.92 |
| New to this | +0.74 |

The headroom result worth keeping: at 80% glass the floor holds **even against a
full-saturation backdrop** (+0.67). The 45% bloom ceiling therefore restrains how
the page looks; it is not the only thing standing between it and an unreadable
state.

## Three things the render caught that the gradients did not

This is the part worth reading, because all three looked correct in the CSS.

1. **The first blooms were clipped.** Radials centred below their element with
   `inset: 0` put most of the bloom outside the box. The gradient was right and
   the page showed almost nothing.

2. **The fix drew a rectangle.** Insetting the box by a percentage stopped the
   clipping and made the box's own edges the visible shape, because the radial
   still had colour where the box ended. Blooms are full-bleed now, with an
   overflow guard on the body because a viewport-width element counts the
   scrollbar on machines that have one.

3. **A hairline contrast failure that looked like nothing.** The Fix band's cyan
   base glow sat directly under the status line and lifted its ground enough to
   take that line to **4.49:1 against a 4.5 requirement**. Not visibly wrong —
   just wrong. The glow's centre moved past the field's base edge.

## And one failure the checking tool invented

The pixel sampler reported the small PDF badge at 1.00:1 — white on white. It was
sampling the card around a small pill rather than the pill itself; the badge
measures **5.42** and always did. The sampler now respects an element's own
opaque background.

Worth recording because a checking tool that cries wolf costs exactly the same
trust as one that misses a real fault, and this one would have sent someone
hunting a defect that was never there.

## Restraint, applied after looking

The first dark fields ran a diagonal from navy to indigo, which painted the
entire lower-right of each field violet and read as a gradient wash rather than
as light rising from a base. Navy now holds the top half of each field and the
violet enters underneath it — saturation concentrated low, as the ruling asks.

## Palette record

The atmosphere range is logged as a decision record, not left in a stylesheet:
violet, indigo, the existing bright teal named for bloom use, and two documented
ceilings. Three hard rules — never text, never a status semantic, never on the
report or app surface. Two are mechanically enforced; the third is held by
pinning the status tokens in the same test, so redefining one to an atmosphere
hue has to edit the guard.

## Payload and motion

**CSS only. +8,733 bytes raw, +3,084 gzipped. Zero images, zero JavaScript.**

Reduced motion is unaffected because there is no motion: the blooms are static
paint. Verified anyway — 0 running animations in the settled state, 102 with
motion allowed, blooms painted identically in both.

## Checks

421 tests, lint and typecheck clean, no horizontal overflow at either width.

## Awaiting the owner

Rounds 9, 9.1 and 10 together: the page treatment, then deploy. Screenshots
provided at both widths, per-section before/after, and one full-page capture so
the composition can be judged whole.
