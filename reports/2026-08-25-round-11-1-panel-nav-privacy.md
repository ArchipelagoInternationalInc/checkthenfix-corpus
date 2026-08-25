# Round 11.1: panel symmetry, nav, privacy line

**Date:** 2026-08-25 · **STAGED, not deployed** — deploy after the owner's look.

## The five notes

**1. Symmetric insets.** 10px above the drop target, 22px below. The extra 12px
was not padding — it was the live status region, which reserves a top margin
whether or not it has anything to announce. Collapsed while idle, so the space
appears with a message and not before. The region stays in the DOM: it is the
`aria-live` target, and removing it would silence upload progress. Now **10/10**
at every width; panel 180px → **174px**.

**2. Both condensed lines run the same width.** Title ink measured **233px**
against the subtitle's **272px**. The strings are fixed lengths, so this is
purely a size ratio. Measured after: **273 vs 272** — one pixel apart.

**3. CTA is a rounded box with no underline**, pinned off through hover, focus
and active rather than only at rest. Worth flagging: the underline itself was
fixed in the previous round at 18:31, so the live review likely predates that
deploy — the note was correct about what was on screen at the time.

**4. Nav de-2005.** Links 15px → **14px**; the group moved right, gap to the
button **114px → 44px**. The hover underline is gone — it drew a 2px rule under
the link, which is exactly the treatment the ruling is about. Hover now shifts
colour to navy and weight to 650. **Focus keeps a real 3px ring**, because
"modern" must not quietly mean "no keyboard affordance". Resting links measure
**6.18:1** over every field at six scroll positions.

**5. Privacy line** replaced with the approved rewrite; both prior wordings
retired and the register entry updated. Counsel still rules on the substance —
encryption, the retention window, what "handled privately" commits us to — not
the phrasing.

### The guard caught my own archive

I first stored both retired wordings as a code constant. One of them contains
"All submitted documents" — and the absolutes guard reads that file, so
archiving the string put the exact phrase the guard exists to exclude straight
back into the codebase. It fired, correctly. The retired wordings live in the
register as prose now, which is the right home for copy nobody may ship again.

## The contrast script was intermittently lying

It reported **seven failures on an unchanged page**, then none on a re-run. Not
first-run, not cold-server — just sometimes. The beyond-viewport capture
occasionally came back not matching the geometry collected moments earlier, so
text sitting on the dark band was sampled against a white ground.

Two fixes did not hold: clearing inherited device-metrics state, then waiting on
`document.fonts.ready` plus a paint probe. Both were reasonable and neither
addressed the actual failure.

What holds is **verification rather than trust**: the capture must prove itself
by showing the dark field as dark at its own centre, or the run retries, and
throws after three attempts. **Six consecutive clean runs.**

A silent bad measurement is worse than a failed script — every number this
project reports rests on these two scripts, and one that is right most of the
time is not a measurement.

## Counts

| | |
|---|---|
| Files / bytes scanned | 34 / 121,825 |
| Claims · house-style · absolutes · dated | **0 · 0 · 0 · 0** |
| Contrast, 1440 and 390 | **35 nodes, 0 failures** |
| Bloom floors | **All met**, both widths |
| Phone fit | **0 overflow** at 430 / 390 / 360 / 320, insets 10/10 at all four |
| Tests | 421, lint and typecheck clean |

The phone-fit fix from the gate report holds.
