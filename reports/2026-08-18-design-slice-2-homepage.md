# Design pass — slice 2 (homepage) + owner refinements

**Session date:** 2026-08-18
**Branch:** `design-pass`
**Commits:** `52ca9fa` foundation · `fe725e5` homepage + screenshot fix · `ca3d628` owner refinements · `381b049` How it works

---

## The finding that matters most

**The screenshot tool had been producing false evidence for the whole project.**

Headless Chrome clamps its window to a **500px minimum**. The capture script
passed `--window-size=390,2200`; measured directly, that produces a page
reporting `innerWidth=500`. So every "390px mobile" screenshot laid out at 500px
and was then cropped to 390 — roughly a fifth of each image sliced off mid-word,
showing a layout that never existed.

This affected the mobile screenshots committed during the deployment phase and
presented as verification of mobile behaviour. `--force-device-scale-factor`
does not work around it: `--window-size` is in CSS pixels and the clamp applies
regardless.

**The pages were never broken.** Measured live at 390px, `scrollWidth` equals
the viewport exactly, with zero overflowing elements. The defect was entirely in
the instrument — the worse kind, because it reported success while showing
something false.

**Fixed** by driving Chrome over the DevTools Protocol and using
`Emulation.setDeviceMetricsOverride`, the only way to lay out below 500px. Node
22 ships a global `WebSocket`, so this cost no dependency and the original "no
Playwright, no 100MB browser download" constraint still holds. The script also
terminates now; the old one hung until timeout on every run because Chrome does
not exit after a `--screenshot` invocation.

Every unfaithful set is now listed in the repo so nobody cites them as evidence.

---

## Counts

| Measure | Before | After |
| --- | --- | --- |
| Tests | 179 | **202** |
| Test files | 19 | 23 |
| Lint / typecheck / build | clean | clean |
| Self-hosted font payload | 0 | 113 KB (2 faces, latin, variable) |
| Runtime font fetches | 0 | **0** (now guarded by test) |

---

## Decisions

**Fraunces over Source Serif 4** for marketing display. Both were live
candidates. The tiebreaker was the owner's binding requirement that the site
read as fresh and current, never as old software: Source Serif 4 reads
institutional-academic, closer to the government-document register the direction
rejects. Confined to H1/H2 on marketing only.

**The `opsz` subset (66 KB) over `wght`-only (36 KB).** Optical sizing is the
reason to use a display serif at all; frozen at `opsz: 14`, Fraunces looks
subtly heavy and wide at 60px — the "enlarged body type" tell.

**GSAP declined** for the ambient hero motion. Three elements on independent
fixed-length loops need no timeline, sequencing or scroll coupling. CSS
keyframes cost 0 KB, start before hydration, and honour `prefers-reduced-motion`
through a media query rather than through JavaScript that must download first.
GSAP would have been the largest dependency on the marketing page — larger than
both typefaces combined — to move three circles.

**The homepage report-preview card renders a real check**, not a mock. The
reference comp fills it with invented figures under categories our checker does
not produce. On the one screen where we claim our numbers come from a named
validator rather than from us, fabricated numbers would contradict the pitch.
Guarded by tests: the counts must sum to the total and the score must match our
own scoring rule.

---

## Measured, not eyeballed

The ambient hero motion was **invisible on first implementation** — max pixel
delta of 3/255 across a full cycle, 271 pixels changing out of ~970,000.

The cause is geometric rather than a tuning error: white circles on a
≤4%-tinted near-white ground have a hard contrast ceiling of 12/9/4 RGB, about
**8 luminance levels in total**. Pushed to the top of that range, the effect now
measures 8/255 max delta with 105,726 pixels moving — a 390× increase in
affected area, and the ceiling those constraints allow.

Motion frames are sampled **deterministically**: every animation is paused and
its `currentTime` set explicitly, so a given frame is identical on every machine
and a re-run is not a diff. The reduced-motion requirement is verified rather
than asserted — the script reloads under an emulated `reduce` preference and
fails if any animation is still running. It reports 0.

---

## A guard that was wrong in both directions

The type-enforcement test (Fraunces strictly H1/H2, no serif in body text)
failed on its first draft with six violations that did not exist: it matched
`sans-serif` as a serif, and matched the CSS property `display: "grid"` as a use
of the display class.

A guard that cries wolf gets deleted, which leaves the rule unenforced. After
correcting it, it was **mutation-tested** — deliberately introducing a violation
to confirm the suite fails — so it is known to catch as well as to permit.

---

## Open for the owner

1. **Reference images are not in the repository.** Two were named for the
   layering rhythm and the ambient circles; neither was ever added. What is
   built follows the written specification. If the rhythm needs to match the
   references, they need to exist somewhere the Builder can see them.
2. **Ambient motion is at its ceiling** under the current constraints. Making it
   more present requires bending one of two stated rules: deepen the hero ground
   past the ≤4% wash, or tint the circles instead of keeping them pure white.
3. **"Why teams choose CheckThenFix"** (four cards in the comp) remains
   unshipped. No bank copy exists, and the comp's version contains a claims
   violation — it credits remediation to "specialists" and validation to
   "independent experts", where validators are software.
4. **The comp's fix-CTA card** remains unshipped: prices are undecided and
   remediation does not exist, so any call to action would promise a product
   that cannot be delivered.

---

## Next

Slice 3 — the report page, the product's face.
