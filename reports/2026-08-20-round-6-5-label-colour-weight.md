# Round 6.5 — condensed labels to peacock blue, Regular weight, Title Case

**Date:** 2026-08-20 · **Branch:** `design-pass` · 306 tests, lint, typecheck and production build clean.

Small round. Everything else from 6.4 unchanged.

---

## 1. Colour — the existing accent, and no new token

The first candidate is `#0E7490`, which this project already has as `--accent`.
It is used **unmodified, and nothing was added**.

> A token invented for a colour that turns out to be the one already in the
> palette is a synonym, and synonyms are how two names for one value drift apart
> later. The alternate earns a token only if it wins.

Both candidates measured against the background they actually sit on — glass over
a wash plate, not pure white, which flatters:

| Colour | | Contrast |
|---|---|---|
| `#0E7490` | accent, **shipped** | **5.20:1** |
| `#1C6E9C` | alternate, one step bluer | **5.42:1** |

Both clear AA. At 22px and weight 400 these are **normal** text, not large — 22px
only counts as large at bold, and the weight just dropped — so the threshold is
4.5:1 rather than 3:1.

Captures: `design/screenshots/2026-08-20-r6-5-labels/accent-400.png` and
`bluer-400.png`, plus 4× label close-ups of each.

---

## 2. Weight — Regular shipped, Light measured

"Illegibly thin" is a question about ink, not taste, so Light was rendered at
final size and measured.

| Variant | Inked | Full strength | **Solid share** |
|---|---|---|---|
| 400 @ 2× | 16.75% | 15.78% | **94.2%** |
| 300 @ 2× | 12.02% | 11.05% | **91.9%** |
| 400 @ 1× | 21.11% | 13.73% | **65.0%** |
| 300 @ 1× | 16.39% | 8.59% | **52.4%** |

*Inked* = share of the label box carrying at least 20% of the ink colour.
*Full strength* = at least 80%. *Solid share* = what fraction of the inked
pixels are solid rather than antialiasing.

**The 2× numbers say Light is fine** — 28% less ink than Regular, still solid
91.9% of the time.

> **The 1× numbers say something different, and they are the ones that matter.**
> Most desktop displays are still 1×, and there a thin condensed stem cannot be
> narrower than a device pixel without going partial. Light renders only **52.4%**
> of its ink at full strength against Regular's 65%, and its full-strength ink
> drops **37%** (13.73% → 8.59%).

Not unreadable — but nearly half the mark is antialiasing, and it is the reading
a 2×-only check would have called healthy. Reported so the pick is made with the
number rather than from a retina screenshot.

**Regular shipped, as ruled.**

---

## 3. Title Case

All six labels:

`Independently Verified.` · `Looks Identical, Reads Differently.` ·
`Honest About Limits.` · `Upload Your PDF.` · `Read Your Report.` ·
`One Step Closer.`

The orientation band's three labels ("The rule", "The problem.", "The tool.") are
owner-approved verbatim from 6.4 and were **not** among the six, so their casing
is untouched. They did move to blue, being condensed labels.

---

## 4. FREE

Red in both the subhead and the closing CTA, unchanged.

`--marketing-red`'s scope narrowed to exactly that one word. It stays a token
rather than being inlined twice, so the next thing wanting this red finds it
there instead of copying a hex out of a component.

---

## 5. Tooling

The comparison script is kept as `scripts/compare-label-variants.ts` rather than
discarded: "owner picks by eye" recurs, and the useful part is not the
screenshots but the two numbers underneath them.

**It had a real bug worth keeping fixed.** `Page.captureScreenshot`'s `clip` is in
**page** coordinates while `getBoundingClientRect` is **viewport**-relative, so
the first run measured whatever happened to be that far down the page and
reported **0% ink for every variant** — four identical, confidently wrong rows.

---

## Guard counts (all unchanged)

| Guard | Result |
|---|---|
| Banned claims | **0 violations** |
| Dated claims | **0 violations** |
| Dash rule | backlog **2** |
| Self-hosted fonts | 3 faces, **109 KB** |

Copy and font suites: 44 tests. Full suite: 306.

---

## Decisions for the owner

1. **Colour** — `#0E7490` (shipped) or `#1C6E9C`. Both pass AA; the alternate is
   0.22 higher in contrast and one step bluer.
2. **Weight** — Regular (shipped) or Light, with the 1× measurement above.
3. Carried from 6.4 and still open: the IFR document permalink, the mobile CTA
   line count, and whether `/terms` and `/privacy` move next.
