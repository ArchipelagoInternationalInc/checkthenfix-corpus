# Design pass — slice 3 (the report page)

**Session date:** 2026-08-18
**Branch:** `design-pass`
**Commits:** `381b049` How it works · `b923f9f` report page

---

## What changed

The report page is the product's face, so this slice was hierarchy rather than
new capability. It now follows the reference comp: one top card carrying the
document's identity on the left, the score numeral in the middle, and the
pass / fail / needs-review tally on the right behind a hairline; then the
executive summary and the score breakdown side by side; then the filter chips
and the grouped check accordions, each with a category glyph.

The honest treatments already in place were deliberately left alone, because
they are the product rather than decoration:

- Breakdown bars are drawn in two segments across **all** checks in a category,
  not only decided ones, so a bar can never read as full while judgement is
  outstanding.
- Any category with open items is labelled **review pending** beside its score.
  "100 / 100" next to "1 needs review" is exactly the blur between *checked* and
  *correct* that the claims rules exist to prevent.
- A category that could not be scored says **Not scored**, never an empty bar
  that would read as zero.
- Status is an icon **and the word** — never colour alone. An accessibility
  company shipping colour-only status would fail the check it sells.

---

## What was deliberately not built

Three things in the reference comp are absent, each listed for the owner:

1. **The fix call-to-action banner** ("We can fix 8 of these 10 automatically —
   $79", with a "Fix with AI" button). Remediation does not exist yet, no price
   has been decided, and the claims audit replaces that button's wording
   regardless. A call to action there would sell a product that cannot be
   delivered.
2. **The entire left sidebar** — dashboard, reports, uploads, templates, team,
   settings, activity log, help centre, plus a seat-count plan widget and a
   breadcrumb. All of it describes a product with accounts. Ours is anonymous
   with no sign-in, and navigation to pages that do not exist is worse than no
   navigation.
3. **The "auto-fix available" summary row**, for the same reason as the banner:
   a count of fixes we cannot perform is a sales claim, not a finding.

---

## A rule that had to be reconciled

The owner's new depth ruling says build depth from nested tinted surfaces. The
standing rule says page-wide tinted washes are marketing-only and the app
surface stays flat paper.

These are reconcilable rather than contradictory: the new ruling changed **how**
depth is built, not **where** washes are allowed. So the report page keeps a
flat paper ground and takes its depth from raised cards on paper and tinted
plates behind grouped lists. No tinted page.

---

## Counts

| Measure | Value |
| --- | --- |
| Tests | 202, all passing |
| Lint / typecheck | clean |
| Screenshots committed | 6 (report, decline, home — two widths each) |
| Sample document | 42 / 100, 21 checks: 9 passed, 12 failed, 0 needs review |

Verified against a real local check and a real decline ("This file could not be
opened as a PDF."), not against fixtures. The 390px captures are true 390px
layouts for the first time, using the corrected capture tool described in the
slice 2 report.

---

## Open for the owner

Carried forward from slice 2 and still open:

1. Reference images for the layering rhythm and the ambient circles are not in
   any repository the Builder can read.
2. Ambient hero motion sits at the contrast ceiling its constraints allow.
3. "Why teams choose CheckThenFix" has no bank copy, and the comp's version
   contains a claims violation.
4. The fix call-to-action needs both a price decision and a shipped remediation
   engine before it can exist.

---

## Next

Slice 4 — the supporting states: staged progress with motion, decline screens,
the big-document email screen, and the report PDF template.
