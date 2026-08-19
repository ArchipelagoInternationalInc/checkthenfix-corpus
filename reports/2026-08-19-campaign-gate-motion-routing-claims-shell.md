# Campaign gate report — motion close-out, honest routing, claims guard, marketing shell

**Date:** 2026-08-19 · **Branch:** `design-pass` · Slices A–E, run consecutively.
**At gate:** 281 tests, lint, typecheck and production build clean; console clean across every capture.

Counts, findings and decisions only.

---

## The three findings that matter most

1. **The estimator has a tail it cannot see.** One page of one document is worth
   19 seconds. No model of page count and file size can predict that, and the
   old model was off by 7.5s mean / 12.7s worst on controlled data.
2. **The dash guard never saw HTML entities.** Every `&mdash;` in JSX had been
   passing the rule silently since it was written. Found by adding the FAQ's own
   dashes to the backlog and watching it change nothing.
3. **Removing the email wall exposed two protections the form was providing by
   accident** — no idempotency guard on the queued check, and no rate limit on
   its route. A button where a form used to be turns both into live holes.

---

## Slice A — sweep final form

**Forward only; the wrap is deleted.** Round 5.4's wrap kept every cell firing
once per cycle. Tidy, and wrong: the wrapped tail finished **beside the
headline**, putting the quietest moment of the gesture in the loudest place.

| Cycle | Origin | Cells | Sweep | Gaps | Fired before origin | Ends |
|---|---|---|---|---|---|---|
| 1 | 102 (r6,c6) | 121 | 16.335s | `[0.135]` | false | 223, row 13 |
| 2 | 84 (r5,c4) | 140 | 18.765s | `[0.135]` | false | 223, row 13 |

**Accepted cost:** a rest of 11.6–18.5s before the next origin (was 5.8s). That
is the honest consequence of "run to the end and stop".

**Checks 20% larger**, path and stroke scaled together about the centre:

| | Before | After | Factor |
|---|---|---|---|
| Span (of diameter) | 19.60% | **23.52%** | ×1.200 |
| Stroke | 3.36% | **4.032%** | ×1.200 |
| Stroke : span | 0.17143 | **0.17143** | unchanged |

**Nothing white travels.** The ramp began and ended at `#f8fbf9` — 0.958
relative luminance against white's 1.0.

> **Max relative luminance in the moving tile: 0.7492** (`#cfe6da`), down from 0.958.

Acceptance, 12 frames across the full 44s cycle with the layer isolated: **zero
interior near-white bands**. Planting one `#f2f6fb` stop back produces **8**,
including runs 134 and 59 columns wide. Edge bands breathe (x=70..120 left,
x=1160..1206 right) and that is reported, not smoothed away.

**H1 contrast re-measured: 7.98:1** at t=14.7s. An earlier version of the check
sampled 7.5s of the 44s journey and reported 8.62:1 — a floor measured over a
sixth of the motion is not a floor.

Captures: `design/screenshots/2026-08-19-design-r5-5-sweep/`.

---

## Slice B — estimator refit, routing reconciled

25 controlled measurements, 10–299 pages, four sources. Bytes held fixed while
pages varied.

```
wall ≈ 2.6997s + 0.013212 s/page + 0.093664 s/MB
```

| | MAE | worst |
|---|---|---|
| Residuals | **0.348s** | **1.214s** |
| Leave-one-out | **0.401s** | **1.358s** |
| Old model, same data | 7.479s | 12.666s |

A megabyte is worth about **seven pages**, not fifty. Images-per-page does not
earn its keep (LOO 0.401 → 0.403).

**Linear deliberately.** sqrt (LOO 0.329) and quadratic (0.298) fit better
in-range; the quadratic turns over at 282 pages and predicts negative time at
1000. sqrt sits below linear past the data, and for a router the dangerous
direction is under-predicting.

**Threshold 150s → 35s**, derived: worst reachable document (1000pp at 50MB) is
20.6s, ×1.4 contention = 28.8s, plus worst residual, round up.

**The sample-first branch is dormant** — nothing reachable is predicted above
35s. It arms if the engine slows ~1.7×. 12s would make it active today.

### Routing at the boundaries

| Pages | Before | Now |
|---|---|---|
| 299 | checked | checked |
| 301 | **declined** "handles up to 300" | checked |
| 999 | declined | checked |
| 1001 | declined | quote-only |

### The tail

FY1920 (172pp, 13.6MB) really takes **25.8–28.9s** — four runs, not contention.
The model says 6s.

| Pages | Time |
|---|---|
| 28 | 4.0s |
| **29** | **23.2s** |

Page 29 carries **16 XObjects and 7 fonts** against 1 and 2–3 on its neighbours.
Averaged over 172 pages that is invisible.

The old test asserting this document estimates above 15s now asserts the
**under-prediction**, deliberately. The tail is bounded by the stage timeout, not
by the estimator.

---

## Slices C, D, E

Full detail in `2026-08-19-slices-cde-lane-claims-shell.md`. In brief:

- **C:** three rulings shipped; email wall removed; idempotency guard added to
  `startQueuedCheck`; rate limit added to `/api/check/queue`; retention verified.
- **D:** claims guard with denial-proximity use/mention mechanism; quote-gating
  rejected because approved copy does not quote; all eight phrases
  mutation-tested; zero violations in current copy.
- **E:** five routes; `/faq` and `/deadline` published, three 404 pending copy;
  entity-decoding hole fixed in both guards.

---

## Delegation log

| # | Call |
|---|---|
| 1 | Hero-motion guard asserted a self-closing tag within 120 chars of the className — a formatting proxy, not the rule. Now asserts "the cell has no children"; mutation-tested. |
| 2 | Cap tests configure a cap rather than asserting the retired 300 default, keeping the mechanism guarded without pinning a removed number. |
| 3 | `MAX_PAGE_COUNT = 0` sentinel written as an explicit `> 0` check — misreading it declines **every** document, which is what happened on the first attempt. |
| 4 | Routing test that encoded the old model's belief rewritten to assert the under-prediction rather than deleted. |
| 5 | Idempotency guard placed in `startQueuedCheck` rather than the route, so it holds for every caller. |
| 6 | Rate limit added to `/api/check/queue` — new surface created by removing the form. |
| 7 | Claims-guard mechanism: denial proximity over quote-gating (reasoning in the module and above). |
| 8 | Claims-guard failure message reports a window centred on the match; against JSX the sentence splitter printed fragments containing no banned phrase. |
| 9 | Unpublished shell pages 404 rather than rendering an empty heading; single registry rather than five scattered conditions. |
| 10 | Held legal/explainer copy specified rather than drafted. |
| 11 | Shared entity decoder for both copy guards, so they cannot drift on what a character is. |

---

## Decisions needed from the owner

**1. The two PM smoothings on the sample-lane partial strings — BLOCKING.**
The ruling approved them "with the two PM smoothings", but the smoothed text did
not reach the Builder. The three partial strings are still marked unapproved
rather than shipped as drafts. Send the smoothed text and they ship.

**2. Sample-lane offer copy — three new sentences,** staged in `proposed.ts`:
- Offer body: *"The full check runs in the background and usually takes a few minutes. You can wait here or come back later. Your report stays at this link."*
- Offer button: *"Check all {M} pages"*
- Optional email label: *"Want the link emailed when it's ready? Leave your address. Optional."*

**3. Threshold: leave the sample lane dormant, or arm it?**
35s is derived and correct, and means the lane never triggers. 12s would route
documents above ~619 pages to a partial result they do not need at current
speeds. Recommendation: **leave it dormant** — the lane is insurance, and the
engine is fast.

**4. Sweep rest gap.** Forward-only leaves the hero still for 11.6–18.5s of each
36s loop, up from 5.8s. Approve by eye, or shorten the loop.

**5. Primary sources — verify the five citations** on `/deadline` (Federal
Register 24 April 2024, 28 CFR Part 35, ADA.gov fact sheet, WCAG 2.1, ISO
14289-1). A wrong citation on a deadline page is a serious error, not a typo.

**6. Copy for the three 404 pages.** `/terms` and `/privacy` need counsel.
`/feedback` needs an address and a definition of "fast" — it is owed by the
approved FAQ, which already promises a channel "answered fast".

**7. Two dash rewrites** (approved FAQ copy predating the rule):
- *"No tool can promise that — compliance is a legal question…"*
- *"…the document's true logic — validators can't judge meaning."*
