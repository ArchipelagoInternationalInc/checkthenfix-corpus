# Campaign gate report — design close-out and checker economics slice 2

**Date:** 2026-08-19
**Slices:** A (sweep origin), B (approved copy), C (supporting states), D (sample-first lane)
**Branch:** `design-pass`
**Status at gate:** 236 tests, lint and typecheck clean, console clean across 12 captures.

Counts, findings and decisions only. No document contents, no secrets.

---

## The short version

Four slices ran without stopping, as briefed. Three things found by building
rather than by reading are worth the owner's attention before anything else:

1. **The report PDF had been shipping the retired brick red since the palette
   landed.** The PDF is a separate rendering path, so the D-016 token change
   never reached it. Every downloaded report carried the old brand colour.
   Fixed.
2. **There was no link styling in the stylesheet at all.** Every inline link in
   the product rendered in the browser's default blue. Fixed, and measured at
   5.2:1 on the real surface.
3. **Slice 1's cost model is wrong about why large documents are slow.** It
   concluded size dominates. Measured properly, page count does. Details below;
   it changes how much to trust the 150s routing threshold.

Four decisions are waiting on the owner. They are listed at the end.

---

## Slice A — sweep origin, corrected ruling

Round 5.3's distance-ranked spread misread the intent. Both variants and the
`SWEEP_ORIGIN_JITTER` flag are deleted. The gesture is the ratified blend again:
strict reading order, left to right, row by row. The only change is where each
cycle starts.

Each cycle now begins at a cell in the H1-adjacent zone and proceeds in ordinary
reading order, **wrapping** from the last cell back to the first until every
cell is struck. The wrap is what preserves the ratified properties exactly:
every cell still fires once per cycle, one stagger apart, so trail length and
cycle duration are untouched.

| Measurement | Result |
|---|---|
| Reading-order mismatches against the wrapped sequence | 0 |
| Sweep span | 30.1s |
| Concurrency (lit cells, sampled) | 9 / 8 / 8 / 8 |
| Cells lit at t=9s | #111–118, consecutive |
| Distinct start cells across four captures | #53, #86, #85 (all inside the zone) |

The t=0 frame is useless for proving the start varies: every cell is still
inside its animation delay and sits at rest, so all cycles look identical there.
A dedicated capture samples 1.2s in, where the trail reveals its origin.

---

## Slice B — approved copy, shipped verbatim

Both owner-approved strings are in: the score explanation on the report page and
the progress-timeout message. Dash backlog fell from 6 entries to 3.

---

## Slice C — supporting states

The states that **replace** the dropzone were still flat white `.card` paper
while the thing they replace is a glass panel on a wash plate. Progress (working
and failed), big-document email capture and the queued screen now wear `.glass`
with `mkt-label` headings.

**The report PDF.** Still drawing `#B3372B`, the retired brick red. A token
change in CSS does not reach a PDF drawn with pdf-lib, so it had quietly kept
shipping the old brand colour on every downloaded report since the palette
landed. Now `#C2334D`, title in inkNavy, header label in accent. Its score
explanation was also still the pre-slice-B wording; the approved string is
applied there too.

**Inline links.** There was no base anchor rule in the stylesheet at all. Every
inline link rendered in the browser's default blue, a colour never in the
palette. It went unseen because buttons carry their own class and the eye
follows the buttons. Links are now accent teal, **measured at 5.2:1 over the
composited glass surface** rather than the flattering on-white figure, and the
underline stays.

**Deliberately not changed.** The decline screen keeps its neutral treatment.
Its own copy says declining is deliberate; painting it in `--fail` would assert
that the document failed when nothing was scored. Only its off-scale `1.75rem`
literal became `var(--text-2xl)`, which is the same 28px.

**Evidence.** Three of these states cannot be photographed in the running
product: the working progress view needs a document that is mid-check when the
shutter opens, and a local check takes about two seconds. A development-only
`/dev/states` route renders the real components in the real wash plate, guarded
server-side. The only thing suppressed is polling.

12 captures at 1280 and 390, each in both motion settings, console watched per
capture. Motion and reduced-motion are byte-identical for the report and decline
pages **because they carry no motion at all** — confirmed by the states page
differing under the same emulation, and by the active dot's animation collapsing
from 1.4s to 1e-05s under `prefers-reduced-motion`.

---

## Slice D — sample-first lane, built and switched off

The lane is finished and **off**. A partial score needs a sentence calling it
partial, and that sentence is the owner's to approve.

### The extractor, and the version that looked right

The obvious implementation copies the first N pages into a fresh document.
Measured against a tagged corpus budget:

| | StructTreeRoot | MarkInfo | Lang | Metadata |
|---|---|---|---|---|
| Source | YES | YES | YES | YES |
| `copyPages` sample | no | no | no | no |
| `removePage` sample | YES | YES | YES | YES |

Four headline accessibility failures, none of them anything to do with the
user's document, all of them ours. It also stamped pdf-lib over the source's
producer. The sample is now built by loading the original and removing the tail.
A test asserts each flag and the producer, and **it was verified to fail** under
the `copyPages` version.

### The measurements correct slice 1

Timed on the Railway container, veraPDF 1.30.2, two runs each:

| Document | Time |
|---|---|
| 163pp / 10.79 MB, full | 6.51s |
| first 50pp, faithful extract / 11.36 MB | 3.88s (40% faster) |
| first 50pp, size-shrunk to 3.64 MB | 3.68s (a further 5%, inside noise) |

Slice 1's cross-sectional fit concluded *"size dominates, a megabyte costs 215×
a page"*. Holding content constant and cutting pages moved the time 40%; cutting
bytes threefold at the same page count moved it by nothing. **Size was a proxy
for how much real content a document has, not a cause.**

Consequences: the faithful extractor is affordable even though it makes the file
bigger; no new PDF tooling is needed, so **no D-004 decision is required**; and
the estimator will over-predict for large-but-simple files and under-predict for
small-but-dense ones. That is a caveat on the 150s threshold, not on the lane.

### Two bugs found by building it

**The engine does not read local files.** It is handed a storage path and
downloads the bytes itself. Passing only the local sample path would have meant
the engine checking the whole document while the row said the result was a
50-page sample — a full score wearing a partial label, the exact inversion of
what the lane promises. The sample is uploaded and both paths move together.

**That upload then created a retention hole.** The purge job removes objects by
the row's `storage_path`, so a sample at any other path would have outlived the
72 hours we promise. Its path is now derived from the source's rather than
stored, and the purge names both.

### Interlock

The report page still renders a partial result exactly as it renders a full one,
because it cannot say otherwise without approved copy. The dangerous state is
someone setting `ROUTING_LANES_ENABLED=true` to try it: every piece correct, and
50-page scores shown for 499-page documents with nothing on screen saying so.

A test now fails if the flag is on while the report page never reads
`samplePages`, and its failure message says what to do. Verified to fire.

Draft copy is quarantined in `lib/copy/proposed.ts`; a guard asserts no
user-facing surface imports it.

### Migration 0006

Additive and nullable: both columns null means "full check", so every existing
row keeps its exact current meaning. Committed, then applied, per
`supabase/README`. Verified: columns present and nullable, constraint installed,
and its expression evaluated against six cases —

| Case | Accepted |
|---|---|
| ordinary full check (null, null) | yes |
| honest sample (50, 499) | yes |
| sample equals source (499, 499) | yes |
| size with no total (50, null) | **no** |
| total with no size (null, 499) | **no** |
| sample bigger than source (500, 499) | **no** |

Security advisors after the DDL: none.

---

## Standing-delegation calls exercised

Non-public-copy, non-dollar judgment calls, per the standing delegation:

1. **Deferred first reseed** of the sweep start cell via a zero-delay timeout
   rather than a synchronous `setState` in the effect body. React's lint flags
   the latter, and it would also mean the first painted frame used one start
   cell and the next used another.
2. **`/dev/states` route**, development-only and guarded on the server.
3. **`poll` / `initialStage` props** on CheckProgress, defaulted so production
   cannot get them wrong.
4. **`cdp.on()`** added to the CDP helper — it discarded event params, and
   console errors cannot be read without them.
5. **Base anchor styling** added. Judged a defect rather than a design change:
   there was no rule at all, so the colour was the browser's, not a decision.
6. **Decline screen left uncoloured**, on the reasoning above.
7. **Migration 0006 applied** after committing, per the repo's stated
   Builder-applies convention.

---

## Decisions needed from the owner

**1. Sweep origin — approve by eye.** Slice A is measured and correct against
the corrected ruling. Whether the varying entry point reads well is a judgment
only the owner makes. Captures are in
`design/screenshots/2026-08-19-design-r5-4-starts/`.

**2. Staged closing CTA — on or off.** Still staged, unchanged.

**3. Sample-lane copy — seven strings.** Drafts are in
`lib/copy/proposed.ts`, written to the compliance rules already (no "ADA
compliant" as a promise, no "guaranteed", no "certified", no implied
affiliation, no em or en dashes). The lane cannot ship until these are ruled on.

- Partial heading: *"This is a partial check."*
- Partial body: *"We checked the first {N} pages of {M}. This score describes
  those pages only, and it is not your document's score. Pages we have not read
  may pass or fail differently."*
- Score label: *"First {N} pages"*
- Offer heading: *"Check the whole document"*
- Offer body: *"The full check runs in the background and takes longer than a
  page load. Leave your email and we will send you the report link when it is
  ready."*
- Offer submit: *"Check it all and email me"*
- Quote-only decline: *"This document has {N} pages, which is more than the
  self-serve checker handles. Documents this size are quoted individually."*

**4. The 300 versus 1000 page conflict.** `checkerConfig.maxPageCount` is 300
and declines anything larger with *"The free checker handles up to 300."*
`routingConfig.quoteOnlyPageCount` is 1000. They disagree about everything
between them. Measured, not read: a 499-page corpus budget is declined
`too-many-pages` in 0.9s today, and scores fine from its first 50 pages. Under
the lane it would be sampled instead of declined — a change to what a user is
promised, so the reconciliation is the owner's.

**5. Remaining dash backlog — three entries.** Each is approved bank copy that
predates the rule, so the replacement wording is the owner's to write:

- `"Is your PDF accessible? — CheckThenFix"` (page title)
- `"Check a document — it's free and…"` (staged closing CTA)
- `"You can leave this page — your report stays at this link."` (progress)
