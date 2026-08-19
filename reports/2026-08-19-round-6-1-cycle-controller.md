# Round 6.1 — cycle controller fix

**Date:** 2026-08-19 · **Branch:** `design-pass` · 289 tests, lint, typecheck and production build clean; 12 captures console clean.

Counts, findings and decisions only.

## The bug, reproduced from the code

Reported live: first cycle plays correctly, then a long dead gap, then one or two
scattered cells fire out of sequence.

**Both symptoms had one cause.** Every cell ran one infinite 36-second CSS loop
and was placed inside it by `animation-delay`.

**The dead gap *was* the loop.** It had to be long enough for the longest
possible sweep — 23.2s from the earliest origin — so a cycle starting later
finished in 16.3s and left up to **18.5s** of timeline that nothing could fill.

**The scattered strikes were the reseed.** Choosing a new origin every 36s
rewrote every cell's delay *mid-loop*: cells already past their strike point
jumped to a new position in the same running timeline and fired again out of
order, and cells that had been resting acquired a delay and fired for the first
time at an arbitrary moment.

Nothing was wrong with the ordering function. The timeline underneath it was
being rewritten while it played.

## The model now

A cycle is a finite performance with a **derived** duration:

```
cycleSeconds(origin) = (CELL_COUNT − origin) × stagger + envelope
```

Each cell runs a **one-shot** 1.135s strike delayed by its rank. There is no
loop, so there is no leftover timeline and a skipped cell owns no slot — the dead
gap is not tuned away, it is structurally impossible. Between cycles the
controller waits `REST_MS = 1500`, then replays from a new origin.

### Mechanism call (standing delegation)

The clean restart is done by **remounting** the cells: the cycle id is part of
each cell's React key, so a new cycle unmounts 224 nodes and mounts 224 fresh
ones, and a mounted element starts its animation at zero.

- *Class toggling* needs a forced reflow between removal and re-add — a
  documented hack that depends on the browser not batching the two writes.
- *Timer-driven strikes* would put 224 timeouts and a wake-up every 135ms on the
  main thread to reproduce what the compositor already does off it.

Remounting is one React commit per cycle, roughly every 18 seconds.

### Envelope values unchanged

Attack is still 185ms, decay still 950ms. Only what they are a percentage **of**
changed — from a 36s loop (0.514% / 3.153%) to the 1.135s strike (16.3% / 100%).
That change *is* the fix, not a retune.

## Acceptance — three consecutive cycles

Observed via `animationstart` events. A still frame cannot answer "did anything
fire out of sequence"; the strikes have to be watched. Cycle boundaries are
detected by a **drop in cell index**, not a timing threshold, so a slow machine
cannot split one cycle into two.

| Cycle | Origin | Strikes | Span | Ends at | Order violations |
|---|---|---|---|---|---|
| 1 | 72 (r4, c8) | 152 | 20.387s | 223 | **0** |
| 2 | 54 (r3, c6) | 170 | 22.803s | 223 | **0** |
| 3 | 101 (r6, c5) | 123 | 16.469s | 223 | **0** |

> **Total out-of-sequence strikes: 0** — across 445 strikes. An earlier
> independent run of 496 strikes, different origins (58 / 70 / 52), also 0.

Three distinct origins in each run; every cycle ends at cell 223.

### Measured gaps

| Gap | Last strike → next first strike | Expected |
|---|---|---|
| 1 → 2 | **2.784s** | 2.770s |
| 2 → 3 | **2.784s** | 2.770s |

**Cycle 3 is the proof the bug is gone:** a late origin gives a 16.5s cycle and
the *same* 2.784s gap. Under the old model that cycle was followed by 18.5s of
nothing.

**The gap arithmetic, stated because it is not exactly `REST_MS`.** Measured from
the last strike's *start*, the gap is one stagger + envelope + rest. The quiet
**after the final fade completes** is **1.649s**: `REST_MS` 1.5s plus the
deliberate 135ms overshoot in the ruling's own duration formula, which ends the
cycle one stagger after the last cell rather than exactly on it. That margin
guarantees the final fade finishes before anything is torn down.

## Reduced motion

**0 strikes in 8 seconds, 0 of 224 cells carrying an animation, settled opacity
0.14.**

The controller is not *started*, rather than started and made fast — the global
CSS rule collapses durations to 0.01ms, which would still run 224 animations and
fire 224 events. Zero events is the difference between "too fast to see" and "not
happening".

## Guards

Five structural clauses pinned in tests; two mutation-tested:

| Mutation | Result |
|---|---|
| Reintroduce `setInterval` reseed | fails *"reseeds nothing mid-flight"* |
| Put an infinite animation back on `.lattice__cell` | fails *"never loops a strike"* |

The gradient's own 44s infinite animation is asserted **untouched**.

One guard needed correcting as written: it first failed on the component's own
comments explaining what the infinite loop used to do. Comments are stripped
before matching — finding those words in prose is not the same as finding them in
code.

## Unchanged, as ruled

20%-larger checks, forward-only reading order, no wrap, gradient layers.

## Note on evidence

The first capture run reported console exceptions on the decline page. Cause: the
decline fixture token had passed its 72-hour anonymous retention window and now
404s — correct product behaviour, not a regression. Re-captured against a live
declined document: clean across all 12.

## Frames

`design/screenshots/2026-08-19-r6-1-cycles/` — two frames from different cycles.
