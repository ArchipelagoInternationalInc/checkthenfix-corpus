# Design round 5.1 — serial strikes, dash guard, headline swap

**Session:** 2026-08-19 · **Branch:** `design-pass` · **Commits:** `4abe12d`, `fb69cb1`

> **Written retroactively.** This round's report was never pushed at the time,
> which broke the standing rule that every session ends with its slice report in
> this folder. Recorded here in full rather than summarised inside a later
> report, so the sequence stays readable.

---

## Sweep — made serial (and then overshot)

Owner review of the previous build: eighteen concurrently lit cells read as a
whole row lighting at once rather than as items being ticked off. The envelope
shape was kept; the ratio changed.

| | Before | After |
| --- | --- | --- |
| Attack | 264 ms | 40 ms |
| Decay | ~1.27 s | 360 ms |
| Stagger | 70 ms | 260 ms |
| Faded when the next fires | ~20% | 72% |
| Concurrently lit | ~18 | **1–2** |

Verified by freezing at six unrelated instants and counting cells above rest:
maximum two, usually one.

**This overshot**, and the following round corrected it — the fast attack read
as a startle-flash and the short decay blinked rather than faded. Recorded
because the miss is the useful part: "serial" and "sequential" are not the same
requirement, and the acceptance count alone did not capture the difference.

---

## The parse guard earned itself immediately

The stylesheet guard added at the end of the previous round caught the *same*
class of breakage one round later: a keyframe replacement left an unbalanced
brace. Previously that symptom cost an hour of misdiagnosis, because the dev
server silently served the last good stylesheet and the page simply looked
unchanged. This time it failed in seconds.

A guard written the day after being burned is worth more than one written in the
abstract, because it encodes the specific shape of the mistake.

---

## Dash rule — guard added, with an honest shape

A copy lint now rejects em and en dashes in user-facing strings.

Two design decisions in it:

- **Comments are stripped before scanning.** This repository's comments are full
  of em dashes and always will be; the rule is about what a visitor reads. A
  lint that fires on prose nobody sees gets muted, and a muted lint enforces
  nothing.
- **It ships with a backlog, not exemptions.** Eighteen already-approved strings
  contain dashes. The replacement wording is the owner's to write, and the
  brief's rewrites arrived as an unfilled placeholder, so inventing substitutes
  would have broken the rule that public sentences come from the bank verbatim.
  The guard therefore passes today and fails on anything new, and entries come
  off as wording arrives.

**Seven of the eighteen were found by the guard rather than by eye**, because
they span source lines and a line-oriented search missed them. One is the
compliance-mandated framing statement, which needs care beyond style.

---

## Headline

The H1 was supplied verbatim and shipped. The subheadline was deliberately left
as the previous sentence: the owner was finalising it pending timing data, so it
was held as a placeholder rather than invented.

---

## Blocked by placeholders in the brief

1. **Font selection** arrived as `[PENDING]`. Nothing was deleted; all four
   faces still shipped.
2. **Dash rewrites** arrived as `[items 1–4 above, verbatim once approved]` with
   no wording.
3. **Closing CTA rewrite** not supplied.

---

## Counts

| Measure | Value |
| --- | --- |
| Tests | 223, all passing |
| Lint / typecheck | clean |
| Dash backlog | 18 entries |
