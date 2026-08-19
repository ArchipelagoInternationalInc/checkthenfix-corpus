# Design round 5 — sequence made audible, headline system, page ground

**Session:** 2026-08-19 · **Branch:** `design-pass` · **Commit:** `23950b5`

---

## Lead finding: the stylesheet had stopped compiling

This round shipped an **unbalanced brace** in the global stylesheet, and the
entire toolchain missed it:

- The type checker and linter do not read CSS.
- **Every CSS assertion in the suite matches text with regular expressions** — a
  broken stylesheet satisfies those perfectly well.
- The dev server fell back to the last good stylesheet rather than failing
  loudly, so the browser kept rendering the **previous** design.

It produced two symptoms that each looked like a different problem:

1. The page "looked like last night". It was not stale caching or a stale
   server — it was literally the previous stylesheet, still being served.
2. The new animation measured **exactly 0/255**. That read as a design that had
   failed, when in fact the CSS under measurement had never compiled.

Both readings were true reports about a file that did not parse. **A regex can
tell you a rule is present; only a parser can tell you the file is valid.**

Now guarded: the stylesheet is parsed with postcss in the test suite, plus a
separate brace-balance check that names the specific failure rather than relying
on a parser error being legible at the wrong hour.

This is worth carrying beyond this project. Text-matching assertions over a
structured file give a *false green* — the most expensive kind, because the
suite actively reassures you while the artifact is broken.

---

## The check sweep now reads

The previous envelope moved a cell from 0.34 to 0.62 opacity — a nudge the owner
watched for and could not see. The new one is a strike:

| | Round 4 | Round 5 |
| --- | --- | --- |
| Resting opacity | 0.34 | **0.14** |
| Peak opacity | 0.62 | **1.0** |
| Attack | ~0.5s, gradual | **264 ms** |
| Decay | ~1.8s | **~1.27 s** |
| Stagger between cells | 42 ms | **70 ms**, strict reading order |
| Peak measured flare | 30/255 across a whole cycle | **35/255**, attributable to a single flare |

Frozen mid-sweep, **eighteen consecutive cells are lit** in a decaying comet —
the leading cell at 0.98, the one behind at 0.94, trailing to rest. That overlap
is the requested effect: one item's fade still visible as the next is struck.

Perceptibility remains a human judgement and is the owner's call. What can be
stated is that the physical magnitude available to that judgement is now several
times what failed before, and the sequence is visible in a **still frame**,
which it was not previously.

---

## Typography

**The body sans is Inter** — self-hosted, variable, `wght` axis, latin subset.
No previous slice report had ever recorded that, which is why it is stated
plainly here.

The serif is retired from the marketing surface. Three replacements are vendored
and rendered side by side at shipped sizes for selection: **Archivo**,
**Space Grotesk**, **Bricolage Grotesque**. One is wired as a placeholder and
switching is a single line.

The font inventory test **names all five faces deliberately**, so that deleting
the two rejects after selection cannot be forgotten quietly. Unused typefaces
are exactly the sort of thing that becomes permanent by never being noticed.

A related finding: **the serif now has no remaining usage anywhere.** It was
only ever on marketing, and the report page never adopted it. It is kept pending
the owner's read rather than removed unilaterally, but it is a fourth deletion
candidate.

A navy was added for the headline, chosen from the owner's range by measuring
against every ground the headline can sit over — from pure white at the hero's
faded edges to the deepest stop of the travelling ramp. Worst case **8.21:1**,
comfortably past the AAA threshold, so the most chromatic end of the range was
taken: the darker candidates drift back toward the black they were meant to
replace.

---

## Page ground

The pale grey below the hero is retired on the marketing surface. One continuous
vertical gradient now runs the length of the page: white through 44%, a very
light tint at 72%, resolving into the approved wash at the foot.

**The hero paints nothing at all.** That is what makes "no line or demarcation
of any kind" literally true rather than carefully matched — two surfaces meeting
invisibly is a thing to maintain, whereas one surface has no seam to maintain.

The deepest stop stops where it does for a measured reason: accent-coloured text
reads **4.80:1** on it, and **4.52:1** one step deeper. Both pass AA; only one
has margin.

The report surface opts out and keeps flat paper, full-bleed, as the standing
rule requires.

---

## Counts

| Measure | Value |
| --- | --- |
| Tests | **208**, all passing |
| Lint / typecheck / build | clean |
| Screenshots | 6 page captures + 6 motion frames + 1 reduced-motion frame + 1 headline comparison |
| Animation libraries | 0 |

Reduced motion verified by measurement, not assertion: reload under an emulated
reduce preference, fail if anything is still animating. Reported 0.

---

## Awaiting the owner

1. **Headline face** — pick one of three from the comparison image; the other
   two get deleted along with their test entries.
2. **Headline wording** — the current bank sentence is in place as a
   placeholder; the swap is one string.
3. **The retired serif** — no longer used anywhere; confirm deletion.

---

## Next

Slice 4 — supporting states: staged progress, decline screens, the
big-document email screen, and the report PDF template.
