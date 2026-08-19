# Design round 5.2 — hybrid sweep, type system shipped, backlog cleared

**Session:** 2026-08-19 · **Branch:** `design-pass` · **Commit:** `48ba070`

---

## Sweep — the third ratio, and the right one

Three attempts, and the shape was never the problem. The ratio was.

| | Round 5 | Round 5.1 | **Round 5.2** |
| --- | --- | --- | --- |
| Attack | 264 ms | 40 ms | **185 ms** |
| Decay | 1270 ms | 360 ms | **950 ms** |
| Stagger | 70 ms | 260 ms | **135 ms** |
| Concurrently lit | ~18 | 1–2 | **8–9** |
| Reads as | a row lighting together | a blink | a decaying trail |

**Acceptance met.** Five frozen mid-sweep samples: **8, 8, 9, 8, 8** — inside the
5–9 band. A representative trail is consecutive and smoothly graded:

```
#103 0.18 · #104 0.30 · #105 0.43 · #106 0.54 · #107 0.67
#108 0.79 · #109 0.91 · #110 0.84 · #111 0.19
```

That gradient is the difference between the three attempts. Eighteen lit cells
had no gradient worth reading; two had no trail at all.

The three numbers are named constants, with the loop-percentage arithmetic
written out beside the keyframes. Keyframe stops cannot be CSS custom
properties, so the division is spelled out rather than hidden — retuning is a
three-line edit, per the brief's request.

---

## Typography settled, and the payload halved

Archivo is wired for real across the headline, section headings and the smaller
label scales.

**Three faces deleted.** Confirmed payload change:

| | Before | After |
| --- | --- | --- |
| Shipped faces | 5 | **2** |
| Total | 209 KB | **81 KB** |

Removed: Fraunces (66 KB), Bricolage Grotesque (40 KB), Space Grotesk (22 KB) —
**128 KB**. The font-inventory test named all five deliberately in the previous
round so that this cleanup could not be quietly skipped; closing it required
editing that test, which is exactly the friction it was built to create.

One assertion was deleted *with* Fraunces rather than left behind: a check that
optical sizing stayed enabled. It existed because Fraunces carries an optical
axis. Archivo has none and needs none between 15px labels and 60px headlines, so
keeping the assertion would have meant guarding a property nothing has.

---

## Copy — five rewrites shipped, backlog down

Applied verbatim. **Dash backlog: 18 → 13.**

The subheadline sets its emphasised word in caps, bold and 1.18em while keeping
it **inside the sentence**, full stop included — emphasis rather than a badge,
which is what the ruling asked for.

**Claims basis for the timing statement**, since it is a public promise: the
recalibrated pre-flight estimator and the measured 6.94 seconds for a 178-page
budget book on the engine container, idle. Both are recorded in the economics
report; the sentence is not an aspiration.

Still on the backlog and untouched, awaiting owner wording: the compliance
framing statement, the report-PDF title, the email subject, the dropzone
instruction, the three "how it works" step lines, the report page's score
explanation, and the progress-timeout message.

---

## Staged, not shipped

The closing-CTA rewrite is built behind a flag that **defaults to off**, with
both sentences in the page so the owner can compare them in the built product
rather than in a document. A review capture is committed separately.

On confirmation the flag and the old sentence get deleted together. A staging
flag that outlives its decision becomes a second source of truth about what the
site says, which is worse than either sentence alone.

The flag was verified back off after the review capture, and the live page
confirmed to render the unstaged sentence.

---

## Counts

| Measure | Value |
| --- | --- |
| Tests | **222**, all passing |
| Lint / typecheck | clean |
| Font payload | 81 KB, two faces |
| Dash backlog | 13 (from 18) |
| Animation libraries | 0 |

Reduced motion verified by measurement, not assertion.

---

## Next

Checker economics slice 2 — the sample-first lane and its on-screen report,
whose label copy is proposed and awaiting approval.
