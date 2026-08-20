# Round 6.4 — deadline correction, condensed label system, band and feedback ship

**Date:** 2026-08-20 · **Branch:** `design-pass` · 306 tests, lint, typecheck and production build clean.

---

## 1. The deadline correction

DOJ's Interim Final Rule (Federal Register, 20 April 2026) extended both Title II
compliance dates by a year. The site still stated the old ones.

A wrong legal date on a page aimed at agencies deciding whether a rule applies to
them is the most damaging factual error this product can make — worse than a
wrong score, because a reader acts on it and has no way to catch it.

### A guard, not a memory

`lib/copy/dated-claims.ts` fails any user-facing sentence presenting a superseded
date as a deadline. Dates decay silently: nothing breaks, no test fails, and the
page keeps looking finished.

**Written before the fix, it found all three places carrying the old dates** —
the homepage callout, `/deadline`, and the staged band. Cheapest possible
confirmation that it works.

### The hard part, again

`"April 2026"` cannot simply be banned, because the **approved replacement says
it** — as the date the extension was *issued*, not as a deadline. Same
use-and-mention problem as the claims guard, same shape of answer: judge what the
sentence does with the date.

A sentence fails when a 2026 date does deadline work with nothing marking it as
the rulemaking.

### Mutation results

| Planted | Result |
|---|---|
| The homepage callout as it shipped | **fires** — superseded pair |
| "Large entities must comply by April 2026." | **fires** |
| "The compliance date is April 2026." | **fires** |
| "DEADLINE: APRIL 2026." (case) | **fires** |
| **Approved band text, verbatim** | **passes** |
| "…interim final rule, Federal Register, 20 April 2026" | **passes** |
| Approved sentence with rulemaking context stripped out | **fires** |

That last row is the one that matters: the exemption is doing work, not waving
everything through.

### What changed on the page

The standalone callout is **deleted, not corrected**. It stated the dates a
second time on a page that now carries them in the band, and two places for a
date that moves is how one of them stays wrong.

`/deadline` no longer restates them either — it is the sources behind them. **Six
citations**, the 2026 IFR first.

> **One citation is weak and says so.** The Federal Register document number for
> the IFR was not supplied and is not something to guess. A fabricated permalink
> on a deadline page is exactly the failure this round exists to fix, and it would
> look *more* authoritative than the honest alternative. It points at the issuing
> agency's index until the owner supplies the document link.

Verified live: `April 2026 and April 2027` appears **0 times** in served HTML;
`April 26, 2027`, `April 26, 2028` and "one-year extension DOJ issued in April
2026" all present.

---

## 2. Condensed label system

**Oswald Variable**, latin `wght` subset, **27.8 KB**.

| File | Face | Size |
|---|---|---|
| `archivo-latin-wght-normal.woff2` | Archivo Variable | 34.1 KB |
| `inter-latin-wght-normal.woff2` | Inter Variable | 47.1 KB |
| `oswald-latin-wght-normal.woff2` | **Oswald Variable** | **27.8 KB** |
| | **Total** | **109.1 KB** |

Inventory test moves to three faces, plus a **payload ceiling** so a heavier cut
shows up in review rather than in a lighthouse run months later.

A condensed face buys size without buying space: the labels run several steps
larger inside layouts that did not move. Archivo has no width axis in this subset,
and a transform can only fake one by distorting letterforms — so a separate file
is the honest way to get condensed.

**Marketing only, and the red is why.** On the report page `--fail` red means a
check failed, and these words beside a score would read as a status claim about
the reader's document. A test keeps the class off the app surface, and
`--marketing-red` is a separate token from `--fail` despite the identical hex.

The type-enforcement guard's closed list widened from three selectors to six. The
count was never the point; the closed list is.

---

## 3. Shipped

- Orientation band, verbatim — flag and staged copy **deleted together**
- Eyebrow, hero paragraph, red `FREE`, two-line closing CTA
- "One step closer." replacing "Keep it.", with its new body
- `/feedback` — `feedback@checkthenfix.com`, answered within two business days.
  The approved FAQ had been promising this channel with nowhere to point.

### One thing the owner should rule on

**The CTA is two lines on desktop and three on a phone.** At 390px the first
sentence cannot fit on one line at this size, and shrinking it to fit would put it
back to the size it was before "bigger and bolder". The ruled break between the
two sentences still holds.

---

## 4. Carried ruling: origins at ≥0.5 mask alpha

A sweep may **finish** in the faded rows, fading out as designed, but starting
there opens a cycle at quarter strength.

| Measure | Result |
|---|---|
| Max dead period | **0.282s** (limit 1s) |
| Sweeps in 60s | 6, origins rows 2/5/3/0/5/2 |
| Out-of-sequence | **0** |
| Strikes off-field | **0** |
| Consecutive origins too close | **0** |

Hero unchanged: moving-tile max luminance **0.7492**, no interior white bands, H1
contrast **7.94:1**.

---

## 5. Guard and backlog counts

| Guard | Result |
|---|---|
| Dated claims | 8 tests, **0 violations** |
| Banned claims | **0 violations** |
| Dash rule | backlog **2**, unchanged |
| Self-hosted fonts | 4 tests, 3 faces, 109 KB |

The dash backlog holds at 2 — both approved FAQ sentences predating the rule.
Nothing shipped this round was on the list, so nothing came off it.

---

## Corpus basis (carried, unchanged)

"Most legacy documents fail" — **0 of 32** corpus documents pass PDF/UA, 19 of 32
untagged. Convenience sample; descriptive support, not a population estimate.

## Decisions for the owner

1. **The IFR document permalink** for `/deadline` — currently agency-level.
2. **The CTA on mobile** — three visual lines, or a smaller first line.
3. Whether `/terms` and `/privacy` (still 404) move next.
