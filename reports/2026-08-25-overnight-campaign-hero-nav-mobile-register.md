# Overnight campaign: hero restructure, navigation, mobile colour, register

**Date:** 2026-08-25 · **Deployed 04:27:23Z.** Six-line verification TRIPLE
GREEN. Review route 200.

**Hero variant pair:** the deployed page shows variant A; `?hero=b` shows the
fallback at the same URL.

---

## Slice A — hero restructure

Both variants meet the acceptance. The owner picks; the loser and the switch are
deleted together.

| | H1 top (of 1080) | H1 lines | Panel | 1920×1080 | 1440×900 |
|---|---|---|---|---|---|
| **A** (primary) | **13.2%** | 2 | 226px | +333px | **+153px** |
| **B** (fallback) | **16.3%** | 3 | 310px | +234px | **+54px** |

Both put the H1 in the top fifth and the complete dropzone panel above the fold
at both desktop sizes. Nothing wraps awkwardly at 390px.

**Two corrections got them there, and both were mine.** Variant A's text block
was first capped at 44ch — a *column* measure, which made the H1 **taller**,
the opposite of the restructure's purpose. Variant B's columns were
centre-aligned, so once the sample card came out of the panel the right column
became the taller one and pushed the H1 down to 24.7%, outside the acceptance.

One detail worth recording: the privacy sentence carries the id the file input
points at through `aria-describedby`. Moving it outside the panel in variant A
meant it had to render **exactly once** — not twice, not nowhere. It is now an
exported component with the id attached, and both variants render one.

## Slice B — navigation

Sticky translucent header: wordmark, five links, free-check button, and a
`<details>` disclosure at 390px rather than a scripted menu — keyboard and
screen-reader operable with no JavaScript, which matters on a page whose whole
promise is that accessibility is not an afterthought.

**The button drops an em dash.** The brief writes it "Check a PDF — FREE", and
em and en dashes are banned in copy site-wide with a guard enforcing it. It
ships as **"Check a PDF FREE"** — the subhead's own approved phrasing minus its
full stop, since a button is a label and labels carry no terminal period.
Flagged here rather than silently reworded.

Labels are shortenings of headings already on the page. `/deadline` and `/faq`
link out; both already gate their own content, so the quarantined explainer body
and draft FAQ answers stay unreachable. **Privacy and Terms are absent from the
footer row, not dead** — their shell gate is false, and they appear the moment
counsel flips it with no edit to any file.

**Contrast measured against what the hero actually paints behind the
translucent bar**, at six scroll positions across every field:
**worst 9.72:1 desktop, 6.43:1 at 390px.**

## Slice C — mobile colour pass

The system was tuned where sections are wide and short. At 390px they are narrow
and tall, and a radial sized in percentages covers a different share of the box.
The "New to this" violet core ended up outside the visible part of its section,
leaving the teal secondary to carry the band: **12.7 ΔE against a 15 floor.**

Geometry only — no new hues, nothing above the documented alpha ceiling.

**390px after:** proof strip **21.1 ΔE** · New to this **19.3** · dark bands
**50°/50°/80°** separation at **67/65/64%** core saturation — all floors met.
Contrast **36 nodes, 0 failures**, worst margin +0.60. Reduced motion **0
running animations**, blooms painted, footer dark, no overflow. Desktop
re-verified unaffected.

## Slice D — the cost table was already produced

**Third request, and it has existed since round 9.1.** Committed as
`MODEL_COST_TABLE.md` and `.json` in this repository, and reported here twice —
in the round 9.1 report and again in round 10.2.

| Document class | n | Median | Max |
|---|---|---|---|
| Agenda / minutes, text only (1-3pp) | 2 | **$0.0131** | $0.0131 |
| Agenda / minutes with images (1-3pp) | 15 | **$0.0245** | $0.0703 |
| Agenda package (4-20pp) | 7 | **$0.0600** | $0.2343 |
| Long report / budget book (20pp+) | 8 | **$0.1628** | $0.1949 |
| **Fleet** | **32** | **$0.0550** | **$0.2343** |

It is not missing. It is not being seen — a corpus commit is evidently not
reaching the pricing sitting. It was sent to the owner directly this time
instead of referenced again.

Standing caveat unchanged: output tokens are calibrated against two documents.
Running the judge now that usage is recorded replaces the estimate with
measurement.

## Slice E — register clearance

| Item | Status | Evidence or gap |
|---|---|---|
| Image handling | **CLEARED** | Drafts flagged only when undescribable, hedged, or below confidence; otherwise written in **unflagged**. The homepage sentence stays out — it claims universal flagging, which is false |
| Reading-order transcript | **NOT CLEARED** | Nothing produces one, in any report type. A build task, not an evidence task |
| Validator on free checks | **CLEARED** | `report-contents.test.ts`, free check asserted specifically, with a negative half so it cannot pass vacuously |
| Score formula + rounding | **CLEARED** | `score-formula.test.ts`. Truncation, not rounding: 9/21 reports **42**. Direction is deliberate — truncation can only understate |
| Scope detection before payment | **HALF** | Detection is real and tested. `lib/payments/` holds one empty `.gitkeep` — there is no charge for it to run *before*. Sentence stays held |

Full evidence in `SUBSTANTIATION.md` in the application repository.

## Delegation log

| Call | Decision | Basis |
|---|---|---|
| Deploy | Merged to `main` and deployed | Standing delegation; the brief asks for the campaign deployed to gate-review and preview |
| CTA wording | "Check a PDF FREE", em dash dropped | House rule bans em dashes site-wide and is guard-enforced; reported rather than reworded silently |
| Hero default | Variant A is the default; B behind `?hero=b` | A is named primary in the brief |
| Mobile colour | Geometry-only pass under a 640px query | A floor was failing at 390px; hues and the alpha ceiling untouched |
| Cost table | Not rebuilt | It exists and passes; rebuilding would have spent the session re-answering a solved question |

## Checks

421 tests, lint and typecheck clean. Copy locked. Hero lattice untouched.
