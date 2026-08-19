# Design round 4 — palette applied, knockout hero, panel grounds

**Session:** 2026-08-18/19 · **Branch:** `design-pass` · **Commit:** `56a80eb`

---

## The 196 explained

The count moved 202 → 196 between the slice-3 and round-3 reports with no
explanation. Here it is.

All six were `lib/marketing/__tests__/hero-motion.test.ts`, deleted wholesale
when the drifting-circles spec it guarded was superseded. Splitting them by
whether that was right:

| Assertion | Verdict |
| --- | --- |
| circles layer at three depths | correctly gone — depths were specific to that spec |
| cycles fall in a 20–60s band | correctly gone — same |
| no JS animation library | should have stayed |
| decorative layer is aria-hidden | **should have stayed** |
| animates transform only, never layout | **should have stayed** |
| fully static under reduced motion | **should have stayed** |

Four of the six describe properties that apply to **any** hero motif, not to
one design. For one round nothing enforced them — the behaviour stayed correct,
but only because the same person happened to rebuild it carefully, which is
precisely what a guard exists to stop depending on.

All four are reinstated and rewritten for the lattice, joined by new guards for
the knockout mechanism and the palette rule. **196 → 206.**

One further test changed rather than being added. The old "accent wash stays
within 12/255 of paper" assertion encoded the superseded ≤4% tint ceiling, and
it failed the moment the owner-approved wash landed — that wash drifts 20/255.
Loosening the threshold would have left a band nobody agreed to, so it now pins
the approved values exactly, which catches unapproved drift in either direction.

---

## Check scale — before and after

The reduction was computed rather than eyeballed, from the round-3 geometry
(a check drawn in a 24-unit viewBox rendered at 42% of the cell):

| | Round 3 | Round 4 | Change |
| --- | --- | --- | --- |
| Check width, as fraction of circle diameter | **24.50%** | **19.60%** | −20% |
| Stroke width, same basis | **4.20%** | **3.36%** | −20% |

The stroke scales with the mark. Left alone it would have become
proportionally heavier as the check shrank, which reads as a different glyph
rather than a smaller one.

---

## The round-2 contrast ceiling is closed

Round 2 reported the ambient motion sitting at a hard ceiling: white circles on
a near-white ground can differ by at most about **8 luminance levels**, so the
effect could not be made more present without bending a stated rule.

**That constraint no longer applies.** Colour now lives in the ground and the
white cells reveal or conceal it as the gradient travels beneath them. The
white-on-white ceiling was a property of the old construction, not of the
palette or the brand.

| | Round 2 (at its ceiling) | Round 4 |
| --- | --- | --- |
| Max pixel delta across a cycle | 8 / 255 | **30 / 255** |
| Pixels changing | 105,726 | **411,152** |

Roughly 3.7× the contrast and 3.9× the moving area, with no rule bent.

---

## Two mechanism traps, both recorded at the point of use

**CSS masks are alpha-based, not luminance-based.** The first knockout drew the
check as a black stroke inside the mask image, on the reasonable assumption that
black would erase. It does not: black is fully opaque, so it keeps its pixels
exactly as white would. The build shipped solid discs with no check at all. The
fix is an SVG-*internal* `<mask>`, which is luminance-based, applied to a rect —
that produces genuine transparency for the outer CSS alpha mask to read.

**The `#` in a data-URI fragment reference must stay percent-encoded.** Written
as `url(#knock)` it terminates the data URI as a fragment identifier and the
mask silently fails to resolve — no error, just an unmasked square.

A third thing is worth recording because it nearly caused a wrong diagnosis. A
standalone probe of the mask appeared to prove it broken while the shipped page
was in fact correct: the probe re-quoted a value containing single quotes inside
single quotes. **Test the shipped artifact, not a copy of it.**

---

## Palette

Applied as approved and recorded as **D-016**. The decision recorded is the
*rule*, not the values: the bright teal measures 2.17:1 on white and may fill,
tint and decorate but never carry text. There is a test for that, and another
that recomputes AA for every text-bearing colour rather than trusting the report
that proposed them.

Retired everywhere, including the places a find-and-replace would miss: the
focus ring, the card shadow, and the failure chip's fill and border, which were
remixed from the new rose rather than left as tints of the retired brick.

---

## Counts

| Measure | Value |
| --- | --- |
| Tests | **206**, all passing |
| Lint / typecheck | clean |
| Screenshots | 6 page captures + 6 motion frames + 1 reduced-motion frame |
| Animation libraries | 0 |

Reduced motion is **verified, not asserted**: the capture script reloads under
an emulated reduce preference and fails if anything is still animating. It
reported 0.

---

## Unchanged, deliberately

No public sentence was rewritten. The fix call-to-action, auto-fix counts and
account sidebar remain parked. The report page was repainted **by token only** —
two-segment bars, "review pending", "Not scored" and icon-plus-word status are
untouched, and the deuteranopia measurement from round 3 is why that matters.

---

## Next

Slice 4 — supporting states: staged progress, decline screens, the
big-document email screen, and the report PDF template.
