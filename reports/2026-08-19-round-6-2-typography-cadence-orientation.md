# Round 6.2 — hero typography, cycle frequency, orientation staged

**Date:** 2026-08-19 · **Branch:** `design-pass` · 291 tests, lint, typecheck and production build clean.

---

## 1. Typography

### H1 leading: **1.06**

Chosen by eye within the ruled 1.05–1.10 range, and set on `.mkt-hero` rather
than on `--leading-tight`. That token is shared by `.mkt-display`, so lowering
it would also tighten every section heading — those are two or three words and
do not need it. The H1 is three long lines stacked and is the only place the
extra tightness reads as intent rather than as a squeeze.

| Width | Font size | Line height | Ratio | Lines |
|---|---|---|---|---|
| 1280 | 60px | 63.6px | **1.060** | 3 |
| 390 | 40.03px | 42.43px | **1.060** | 3 |

### Dropzone

Heading is two explicit lines at `var(--text-lg)` — **18px, up from 17px**.
Subline is two explicit lines.

Both breaks are block spans, not `<br>` and not a hope that the column happens
to be the right width: the break is part of the reading, so it must not depend
on the viewport. The sentences themselves are unchanged approved copy.

Captures: `design/screenshots/2026-08-19-r6-2-type/` — `hero-desktop-1280.png`,
`hero-mobile-390.png`.

---

## 2. Cycle frequency

`REST_MS` **1500 → 400**.

| Gap | Last strike → next first | Quiet after final fade | Expected quiet |
|---|---|---|---|
| 1 → 2 | 1.700s | **0.565s** | 0.535s |
| 2 → 3 | 1.717s | **0.582s** | 0.535s |

The quiet is `REST_MS` plus the deliberate 135ms overshoot in `cycleSeconds` —
the cycle ends one stagger after the last cell so the final fade cannot be cut
off. Measured overshoot beyond that is 14–47ms of timer and frame granularity.

The test that pinned `REST_MS` at exactly 1500 now asserts the **ruling**
instead: short enough to read as continuous. Pinning the literal made every tune
a test edit and said nothing about whether the value was right.

### First-cycle origin — pinned, not skipped

Computed from a `Range` around the headline's first character, mapped through
the grid's own cell size.

> **The F is outside the zone, and the zone wins.** Measured, the glyph sits at
> raw **row 2, column 1** of 16. Columns 0–3 are inside the left edge fade,
> where a white cell over white ground is invisible — which is the exact reason
> `H1_ZONE` starts at column 4.

So "nearest the F" resolves to the nearest **legal** cell: **52** (row 3, col 4).
Pinning literally to column 1 would open every page load with a strike nobody can
see. Both coordinates clamp at desktop widths, so the pin currently lands on the
zone corner; it varies with viewport rather than being a constant.

Verified across runs: cycle 1 origin **52 = MATCH**.

### A measurement bug found and fixed

`verify-sweep-cycles` attached its listener **600ms after load** and therefore
missed the opening strikes of the first cycle — about four at a 135ms stagger —
then reported the fifth cell as the origin. That is how a pinned origin of 52
first read back as **57**: the pin was working and the instrument was late.

It now installs via `Page.addScriptToEvaluateOnNewDocument`, before any page
script, listening on `document` because the grid does not exist yet.

Three cycles, **zero out-of-sequence strikes**. Reduced motion still 0 strikes,
0 of 224 cells animated, settled opacity 0.14.

---

## 3. Orientation copy — staged, not shipped

Hero paragraph and the three-block **"New to this?"** band, built on the round-5
system (wash plate, glass cards, mkt-labels, no new colours) behind
`STAGED_ORIENTATION_BAND`, default **off**. Verified absent from the served HTML
with the flag off.

The wording is Builder placeholder of representative length so the **layout** can
be judged; the PM supplies final text. The band renders a visible **"staged for
review: wording pending"** marker whenever the flag is on — a review capture of a
plausible-looking band is exactly the artifact that gets approved by accident
three weeks later.

Captures: `design/screenshots/2026-08-19-r6-2-orientation-staged/`.

### The quarantine guard is tightened, not bypassed

It banned any user-facing import of `proposed.ts` outright. That was right while
nothing staged needed to be seen, and wrong the moment the owner asked to review
a band **in place** — a blanket ban would have forced the strings inline, giving
two sources of truth about unapproved copy.

The rule is now what the purpose always was: **a user-facing file may import
staged copy only if it is gated behind a staging flag.** Strictly stronger, because
it also catches an inline staged string that forgot its gate. Mutation-tested: an
un-gated component fails it.

Its first version passed a deliberately un-gated component because the
component's own doc comment mentioned the gate **by name**. Comments are stripped
now. That is the third guard this fortnight to be satisfiable by prose describing
the thing instead of code doing it.

### Guards over the staged strings

Claims guard and dash rule run over all **16** staged strings (orientation +
sample lane): **clean**.

---

## 4. Corpus basis for "most legacy documents don't"

For the report, not the page.

| Measure | Result |
|---|---|
| Documents in `corpus/INTAKE.tsv` | 32 |
| Untagged | **19 of 32 (59%)** |
| Pass PDF/UA under veraPDF | **0 of 32 (0%)** |

**This is a convenience sample** collected for evaluation, not a random sample of
public-sector documents. It supports the claim descriptively and is **not a
population estimate**. If the owner wants the sentence to carry a number, the
number needs a defensible sample first.

---

## Decisions for the owner

1. **Leading 1.06** — approve by eye, or nudge within 1.05–1.10.
2. **Dropzone heading at 18px** — one step up as ruled; say if it wants another.
3. **The pin lands on the zone corner at desktop widths** because the F is outside
   the zone in both axes. Accept, or widen `H1_ZONE` toward the headline (which
   would put opening strikes in the edge fade).
4. **Final orientation wording** from the PM; placeholder and marker are deleted
   with the flag when it lands.
5. Whether "most legacy documents don't" should carry the corpus number at all,
   given the sample caveat above.
