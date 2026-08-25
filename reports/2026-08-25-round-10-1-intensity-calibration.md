# Round 10.1: intensity calibration

**Date:** 2026-08-25 · **DEPLOYED 03:20:35Z.** Rounds 9, 9.1, 10 and 10.1 all
reached production together, released by the owner accepting round 10's
structure and asking for the deployed page.

## The bug that made this round hard, and it was mine

Both measurement scripts collected element boxes at a 900px viewport, then took
the screenshot after resizing to full page height. **The resize reflows the
page**, so every box was stale by however far its section had moved.

That single fault produced every confusing result in this round: a dark band
with an obvious cyan glow reporting one hue family; a light section reporting a
navy "bloom core" with a ΔE of 80; and the contrast audit swinging from **0
failures to 14 with no readability change between the runs**.

I spent several passes tuning the design to satisfy a broken instrument before
checking the instrument. Layout is now fixed before anything is measured, in
both scripts, and every number below comes from the corrected order.

Three further measurement corrections, each recorded where it was made:

- **Bloom cores are found by Lab chroma, not HSV saturation.** HSV reads 81% on
  plain dark navy, so ranking by it made each field's own base colour the "core"
  and the real glows were never found.
- **Hue families are clusters, not the top two pixels.** Taking the two most
  chromatic samples kept returning the mid-blue *transition* between two glows —
  reporting 31° while a cyan and a violet sat 76° apart on the same band.
- **Light-section sampling is restricted to light ground.** A light bloom is a
  tint on white; a near-black pixel inside its box is a card border or spill.

## Intensity floors — all met

| Section | Measure | Result |
|---|---|---|
| Proof strip | ΔE2000 vs base white | **19.5** · 58% coloured coverage |
| New to this | ΔE2000 vs base white | **31.5** · 82% coverage |
| How it works | ΔE2000 | 14.4 — *informational, not gated* |
| The Fix | hue separation / core saturation | **60°** / **66%** · lum delta 0.252 |
| Closing CTA | hue separation / core saturation | **60°** / **65%** · lum delta 0.894 |
| Dropzone base | hue separation / core saturation | **70°** / **63%** · lum delta 0.788 |

How it works is deliberately outside the floor: the standing ruling calls for the
lightest treatment on the page there. It is measured and reported, not gated.

## Contrast over the hotter backdrops

**47 rendered text nodes, 0 failures.** Analytic matrix re-run at the raised
ceiling: **16 pairs, 0 failures, +1.11 smallest margin.**

**The glass floor did not have to rise.** 80% had already been measured against
a full-saturation backdrop in round 10, which is why the bloom ceiling could go
from 0.45 to 0.72 without touching it.

One text colour did change. The Fix band's status line went from bright teal to
**white**: the hotter cyan glow reaches the band's centre-bottom and lifted the
ground beneath it into the same teal family as the text, taking it to **3.6:1
against a 4.5 requirement**. Two teals at similar lightness is a contrast problem
that cannot be tuned away without dimming the glow this round exists to brighten.

## What changed, and what did not

Chroma and alpha only, one variable family as instructed. The two glows in each
dark band are now **spatially separated**, because stacked over one ground they
blended into a single blue-violet and the band carried one hue where two were
drawn.

**Hero grid 62/38.** The H1 drops from four lines to **three** at desktop. No
wrap problems and no overflow at 1440, 1200, 960 or 390 — the panel's text breaks
on explicit spans and lost nothing to the narrowing. The 3/5–2/5 fallback was not
needed.

Geometry, copy and the hero animation are untouched. Payload remains CSS only.

## One deliverable not produced

The side-by-side acceptance capture could not be composed. The references
arrived pasted into the conversation rather than as files, so they could not be
written to disk — and they are another designer's published work, which the
public corpus must not carry in any case.

`scripts/compose-reference.mjs` is committed and does the job in one command
once the two images exist as files. The page halves were captured at matched
width and sent to the owner directly.

## Verification

Six-line verification against the deployed URL: **TRIPLE GREEN.** Review route
200. 421 tests, lint and typecheck clean.
