# Slice B — estimator refit on controlled data, routing reconciled

**Date:** 2026-08-19 · 246 tests, lint and typecheck clean.

## 1. Why the old fit was wrong

It read *"size dominates: a megabyte costs 215× a page"* out of the corpus cost
table. Against controlled measurement it is off by **7.48s mean, 12.67s worst**.

The corpus table could not have said otherwise:

- 22 of its 29 usable rows are 1–6 page documents
- only five exceed 16 pages
- its three largest were **declined** at the 300-page cap, so their ~2.3s is the
  cost of *refusing* a document, not of checking one

Size correlated with content volume across that mixture and took the credit
pages had earned.

## 2. Controlled design

25 measurements on the Railway container, 10–299 pages, four source documents.

Extracting the first N pages leaves orphaned objects behind, so **bytes stay
fixed while pages vary** — an accident of the extractor that happens to be
exactly the control needed. Minimum of two runs per point: the container is
shared, so the floor is the signal.

```
wall ≈ 2.6997s + 0.013212 s/page + 0.093664 s/MB
```

| | MAE | worst |
|---|---|---|
| In-sample residuals | **0.348s** | **1.214s** |
| Leave-one-out | **0.401s** | **1.358s** |
| Old model, same data | 7.479s | 12.666s |

A megabyte is worth about **seven pages**, not fifty.

**Images-per-page does not earn its keep.** Adding it moves leave-one-out from
0.401 to 0.403 — nowhere. Page count already carries it.

### Linear deliberately, though curved fits better

| Model | LOO MAE | Verdict |
|---|---|---|
| pages + MB (shipped) | 0.401 | monotonic, safe to extrapolate |
| sqrt(pages) + MB | 0.329 | better, but predicts lower past the data |
| pages + pages² + MB | 0.298 | **unusable** — turns over at 282 pages |

The quadratic's squared term is negative: it predicts a 1000-page document
finishing in *negative* time. sqrt is monotonic but sits below linear everywhere
past the data, and for a router the dangerous direction is under-predicting.
Linear is chosen as the conservative extrapolant, at a cost of 0.07s of
cross-validated error.

## 3. Threshold re-derived: 150s → 35s

The worst document the checker can ever see is the quote cap (1000 pages) at the
upload cap (50 MB). The model puts that at **20.6s**. Contention inflates a run
up to 1.4× → 28.8s, plus the worst cross-validated residual. Round up: **35s**.

The old 150s was seven times beyond anything that can physically reach the
checker.

**Consequence, stated plainly: the sample-first branch is dormant.** Nothing that
can reach the checker is predicted above 35s. It arms if the engine slows ~1.7×.

| Threshold | First bites at | Status |
|---|---|---|
| 150s (old) | ~11,064 pages | unreachable |
| 35s (shipped) | ~2,370 pages | unreachable |
| 20s | ~1,224 pages | unreachable |
| 12s | ~619 pages | **active** |

Tests assert the dormancy, so a slowdown surfaces there rather than in
production.

## 4. The estimator has a tail it cannot see

This is the finding of the slice.

FY1920 (172pp, 13.6MB) really takes **25.8–28.9s** — four runs, not contention.
The model says 6s. No model of page count and file size can do better:

| Pages | Time |
|---|---|
| 28 | 4.0s |
| **29** | **23.2s** |

**One page is worth 19 seconds.** Page 29 carries **16 XObjects and 7 fonts**
against 1 and 2–3 on its neighbours. Averaged over 172 pages that is invisible —
the document's images-per-page reads as 1, unremarkable.

Aggregates cannot predict a cost that lives in a single object.

The old test asserting this document estimates above 15s now asserts the
**under-prediction**, deliberately. Weakening it until it passed would have
deleted the only evidence in the suite that the tail exists.

The tail is not the estimator's job — the **stage timeout** is what bounds a
runaway check.

**Recommended next experiment (not shipped):** a max-per-page resource signal
rather than an average. One pathological document is n=1, and fitting a
coefficient to it would be worse than not having one.

## 5. Routing reconciled

The 300-page hard decline is retired. `MAX_PAGE_COUNT` defaults to 0 = no cap,
and the pipeline guard is written as an explicit `> 0` — misreading that
sentinel declines **every** document, which is what happened on the first
attempt.

| Pages | Before | Now |
|---|---|---|
| 299 | checked | checked |
| 301 | **declined** "handles up to 300" | checked |
| 999 | declined | checked |
| 1001 | declined | quote-only |

Cap tests now configure a cap rather than asserting a retired default, so the
mechanism stays guarded without pinning a number the owner removed.
