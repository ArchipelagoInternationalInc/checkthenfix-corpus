# Round 9.1: owner visual picks, and the cost table

**Date:** 2026-08-24 · Still on `design-pass`, **not deployed** — round 9 is
awaiting owner review and 9.1 sits on top of it.

## The cost table (the item that outranked polish)

This is the cell marked PENDING in the pricing package: model cost per document
for the judgment passes, at current rates.

| Document class | n | Median | Max |
|---|---|---|---|
| Agenda / minutes, text only (1-3pp) | 2 | **$0.0131** | $0.0131 |
| Agenda / minutes with images (1-3pp) | 15 | **$0.0245** | $0.0703 |
| Agenda package (4-20pp) | 7 | **$0.0600** | $0.2343 |
| Long report / budget book (20pp+) | 8 | **$0.1628** | $0.1949 |
| **Fleet** | **32** | **$0.0550** | **$0.2343** |

Scope is the AI judgment passes only — reading order, headings, tables,
language, alt text. Validator compute, the remediation engine, storage and the
container are separate lines and are not in these numbers.

### The finding worth acting on

**Cost scales with pages judged, not pages in the file.** A 507-page budget book
costs $0.19; a dense 16-page agenda package costs $0.23 and is the most expensive
document in the corpus. Page selection caps what reaches the model at lead pages
plus the image-bearing pages needed to hit the alt-text target, ceiling 25.

Per-page pricing would therefore overcharge long documents and undercharge dense
short ones, relative to what they actually cost.

### The run logs did not contain tokens

The brief asked for this from the Phase 1 and Task 10 run logs. **They do not
record tokens.** The model client read the response's content and discarded the
rest, including the usage block the API returns free on every call. Every
judgment run to date happened without anyone writing down what it cost.

Fixed at the cause: the client now records usage, the judge writes per-document
totals into each judgment log, and the log type carries them. Counts only — the
standing rule that document content never reaches a log is not relaxed by this.
That fix cannot be applied backwards, so this round reconstructed the cost.

### Which half of the reconstruction to trust

**Input tokens are counted, not estimated.** The deterministic half of the
pipeline was re-run for real, the prompt builders were imported from the
pipeline so the table measures the same strings the pipeline sends rather than a
re-typed copy, page images were rendered at the pipeline's own DPI, and every
prompt went through the token-counting endpoint.

**Output tokens are the weaker half and are labelled as such.** The model's
replies were never stored, so they are reconstructed from the recorded decisions
— which are the model's output, parsed — and calibrated against the two
documents that have real judgment logs.

**The first pass was wrong in the cheap direction.** Uncalibrated, it understated
the fleet by about a third, because output prices at five times input. The
correction factor is **x4.50**, the worst case rather than the average of the two
recorded documents, and it is printed in the table rather than buried.

**Two documents is a thin calibration basis.** Stated rather than hidden. Running
the judge over a handful of documents now that usage is recorded replaces the
derived column with measurement.

## Visual picks

**CTA variant B1 ships.** Dark field, white sentence, FREE in the bright teal.
The staged variants are deleted. The palette red is not available here and cannot
be: it measures 2.33:1 against this ground where the bright teal measures 5.83.

**The result card runs the full width of the dark panel.** The inset was left
over from when the hero stack sat on a light wash and the card needed to read as
a separate artifact resting behind the dropzone. On the dark panel it read as a
layout accident, exactly as observed. Verified in the DOM: equal 12px insets both
sides, card width equal to the dropzone width.

**The band heading takes the light mint**, measured before shipping because a
pale tint on a dark ground is the kind of choice that quietly lands at 3:1. It
does not: **11.34** at the gradient's dark end and **10.92** at its light end,
against white's 12.64 and 12.18. Both are AAA at any size, and the mint is about
ten percent softer than white, which is the intent. No comparison variant was
staged, because the condition for staging one — the wash reading too faint — is
not met by the measurement. The screenshot still shows it, so the owner can
overrule by looking.

## Checks

411 tests, lint and typecheck clean. Guard counts unchanged. No copy moved this
round.

## Still awaiting the owner

Round 9's page treatment, and then deploy. Both rounds are on the branch.
