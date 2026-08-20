# Round 6.6 — Title Case verdict, eyebrow face, label scale

**Date:** 2026-08-20 · **Branch:** `design-pass` · 306 tests, lint, typecheck and production build clean.

---

## 1. The Title Case discrepancy — verdict

> **Stale view, not a missed path.** And the reason the view was stale is worth
> more than the verdict.

### What was checked

**Source:** exactly **one** definition of the six labels
(`app/(marketing)/page.tsx`), already Title Case. The only lowercase copies live
in `GO_TO_MARKET_AND_WEBSITE.md` — the copy bank, never rendered. No duplicate
component, no PDF template carrying them, no staged variant.

**Render, from a cold start** — dev server killed, `.next` deleted, cache
disabled, hard load:

| Label | DOM text | text-transform | Family | Size | Weight | Colour |
|---|---|---|---|---|---|---|
| 1–6 | **Title Case** | `none` | Oswald | 22px | 400 | `rgb(14,116,144)` |

Nothing was transforming them. Source and render agree.

### So where was the owner looking

> **Every one of the last 20 Vercel deployments is in state `BLOCKED`.**

Not *failed* — **blocked before building**: `buildingAt`, `ready` and `createdAt`
are the same instant, and `errorLink` points at team-configuration
troubleshooting.

That covers every push back through **round 5b**. Nothing from rounds 5b, 6.0,
6.1, 6.2, 6.3, 6.4 or 6.5 has ever been viewable on a preview URL.

The owner's only view of any of this work is their own dev server — which is why
a stale one shows round 6.4's red, sentence-case labels. That is the mechanism,
not a guess.

For completeness: `main` is 33 commits behind `design-pass`, and `release` — the
branch Vercel Branch Tracking was pointed at — is **86 behind** and described in
its own commit as "deliberately empty until launch".

### Closing the class

There is no render path to fix, so the class that needs closing is **on my
side**. These reports have said "shipped" and "live" on the strength of localhost
while no deployment had built in twenty commits. "Shipped" has meant *"on
`design-pass` and verified locally"* — and I did not say so.

Recorded in the handoff. Deployment state is now something to state rather than
assume.

**Owner action:** unblocking deployments is an account-level change with billing
implications, so it is not something I should do unasked. The `errorLink` on the
deployment points to
`vercel.com/docs/deployments/troubleshoot-project-collaboration#team-configuration`.

---

## 2. Eyebrow

Oswald **Regular**, sentence case as written.

Weight 400 rather than the 700 it carried in Archivo: a condensed 700 at this
size reads as a second headline, and an eyebrow that competes with the H1 is not
an eyebrow.

---

## 3. "One Step Closer." → "The Result."

Title Case like its peers. Body unchanged.

---

## 4. One step larger — `--text-xl` → `--text-2xl`

| Element | Before | After |
|---|---|---|
| Eyebrow (desktop) | 22px | **28px** |
| Eyebrow (390px) | 17px | **20px** |
| Proof-strip labels ×3 | 22px | **28px** |
| How-it-works labels ×3 | 22px | **28px** |
| Orientation band labels ×3 | 22px | **28px** |

The band labels use the same class and were kept in the system rather than left
behind at the old size.

### Wrapping

Measured at both widths: **every label sits on one line except the longest**, and
the size increase pushed that one onto two.

**The greedy break was the awkward one** — `Looks Identical, Reads /
Differently.` splits the second clause. `text-wrap: balance` moves it to the
comma, where the sentence already pauses:

| Width | Line 1 | Line 2 |
|---|---|---|
| 1280 | `Looks Identical,` | `Reads Differently.` |
| 390 | `Looks Identical,` | `Reads Differently.` |

Verified by reading actual line contents from `Range` client rects rather than
judging a screenshot. It costs nothing for the labels that still fit on one line.

The type-enforcement mapping is asserted **per selector**, so the eyebrow moving
from display to condensed had to be stated rather than absorbed by a count.

---

## Guard counts (all unchanged)

| Guard | Result |
|---|---|
| Banned claims | **0 violations** |
| Dated claims | **0 violations** |
| Dash rule | backlog **2** |
| Self-hosted fonts | 3 faces, **109 KB** |

Copy and font suites: 44 tests. Full suite: 306.

Captures: `design/screenshots/2026-08-20-r6-6-labels/` at 1280 and 390.

---

## Decisions for the owner

1. **The blocked deployments** — nothing has been previewable since round 5b.
2. Carried open: label colour (`#0E7490` shipped vs `#1C6E9C`), weight (Regular
   shipped vs Light), the IFR document permalink, the mobile CTA line count, and
   `/terms` / `/privacy`.
