# Competitor output test — PARTIAL
## 2026-09-17 · Builder session · investigation only; nothing about the product changed

The competitor's half of this test is **not done**, for one reason stated plainly
below. Our own half is complete and measured. Two side findings are worth more
than the part that is missing.

## 1. Terms of service — the gate passes

Read first, as instructed. Their terms, last updated March 2026, do **not**
restrict benchmarking, competitive analysis, evaluating output quality, use by a
competitor, or publishing findings. Nothing in them forbids this test.

Three clauses bear on how it is run:

- **Output is ours.** Their terms state that the remediated PDFs they generate
  from your documents "are yours to use however you like." Scoring and reporting
  on their output is explicitly permitted, not merely unforbidden.
- **Scan limits may not be circumvented.** Matches the owner's constraint
  exactly. One account, one scan, no workaround.
- **Reverse-engineering their models is prohibited.** We do not do this. Running
  a validator over a finished PDF is not model extraction, and nothing in this
  test probes how their engine works.

One clause needs the owner's judgment rather than the Builder's: their terms
forbid account sharing. Whether an agent logging in with the owner's credentials
counts as sharing is a reading the owner should make. It points the same way as
section 5 regardless.

## 2. Plan and upgrade path — public pages only

From their public pages, no account required:

| | |
|---|---|
| Free | An audit tool needing no account; and the first remediation free after signing up |
| Paid | Engagements "starting at $100/month", scoped to backlog and volume |
| Per document | "From $1" |
| Published tiers | None on the pricing page — custom scoping, not fixed plans |

**A discrepancy worth recording:** their blog and comparison pages describe
published plans starting at $29.99/month with no custom quotes. Their pricing
page and home page describe custom-scoped engagements from $100/month. Both are
their own copy. Which applies to this account cannot be settled from outside the
app, and **nothing was purchased.**

What the free plan permits *inside the app* is not recorded here, because that
needs the account.

## 3. Dashboard scoring-method note — NOT captured

The brief quotes a dashboard note about a scoring-method change on 20 July 2026,
and earlier scores being AI estimates not comparable to newer compliance scores.

**This session did not see it and cannot confirm it exists.** It is inside the
authenticated dashboard. It is recorded here as not captured rather than
described as captured, because the difference matters: if that note is real it
substantially weakens any comparison against their older published numbers, and
that is worth confirming with a screenshot rather than repeating.

## 4. The table — two of four columns

Canonical document, single page. Same validator for every column: veraPDF
1.30.2, profile ua1 — the version and profile named on our own reports.

| | our before | our after | their after | Acrobat |
|---|---|---|---|---|
| Score | **42** | **100** | not obtained | n/a |
| Tagged | no | yes | — | — |
| Structure tree | absent | present | — | — |
| Structure elements | 0 | **123** | — | — |
| Element mix | — | 1 Document, 1 H1, 10 H2, 111 P | — | — |
| Extracted text | 1,810 chars | **1,810 chars** — identical | — | — |
| Page count | 1 | 1 — unchanged | — | — |
| Visual change vs original | baseline | **0.0% of pixels; mean delta 0** | — | — |
| Their own claim | — | — | not obtained | — |
| What our checker says about their file | — | — | not obtained | — |

The one number there that a validator score cannot express: our remediation
changed **no pixels at all** and **lost no characters**. The document a clerk sees
after our pass is the document they uploaded, to the pixel, with a reading order
added underneath it.

For the record, their published claims about their own output, taken from their
public pages: WCAG 2.1 AA compliant, Section 508 ready, PDF/UA-1 certified, and
validated against PAC 2024 with zero critical errors. Recorded as their wording,
not assessed — assessing it is exactly what the missing column is for.

## 5. Why the competitor half is missing

Completing it requires logging into their app, which means entering the account
password. The Builder does not enter passwords, on any site, for any reason —
including when the owner supplies them and authorises the use. So steps 2
(in-app), 3, 4 and 6 stop here.

This is not a tooling failure and not a limit to be worked around by another
route. The owner performs the login; the rest of the test then runs in the same
session.

**The free scan is unspent. No second account was created. Nothing was bought.**

The instrument that will score their file is already written, committed, and
proven against our own two columns, so the single scan will not be spent on a
harness still being debugged.

## 6. Acrobat column — absent, as expected

`acrobat-output/` did not exist in the private corpus at the time of this test,
locally or on the server. Per the brief, this stops there. **The folder was not
created.**

## 7. Separate line of evidence — which database our scripts run against

The question assumed lock-testing scripts exist. **They do not.** Nothing in the
repository tests locking; the one file whose name suggested it is a feature-flag
interlock that touches no database.

Searching for the real answer produced something more useful, and it is not
reassuring:

- **No automated test touches a database at all.** No test file constructs a
  database client, so `npm run test` cannot reach any project, live or otherwise.
  That is the good half.
- **There is no throwaway and no disposable copy.** Exactly one project exists
  for this product, with **no branches**. There is nothing to run against but the
  real thing.
- **Two of the three config environments point at that same one project.** Their
  database URLs are byte-identical, confirmed by fingerprint without printing
  hostnames. The third does not define the variable at all, so where production
  reads it from was not established from this machine.
- The one destructive script — the retention purge, which deletes rows — is
  guarded by an **optional** `--dry-run` flag and nothing else: no environment
  check, no confirmation, no refusal to run against production. Its own header
  notes it is the same code path production uses, which is true and is also the
  risk.

The honest answer to the question as asked: **neither.** Scripts run against the
live project, because it is the only project there is. No harm is recorded and no
incident is implied — but the safety the question was checking for does not
currently exist, and one command typed without `--dry-run` is the whole margin.

That deserves its own session. It is unrelated to the competitor test and surfaced
only because the question was asked.

## What one document cannot determine

Even with the missing columns filled, one page of one document supports very
little:

- It cannot establish that either engine is better in general. A single agenda
  exercises headings and paragraphs. It says nothing about tables with merged
  cells, multi-column layouts, scanned pages, forms, or long budget books — the
  documents where remediation gets hard and where engines diverge most.
- It cannot compare reliability. One run shows one outcome, not a failure rate.
- A perfect validator score is not a usable document. Both can be true at once:
  a file can satisfy every machine-checkable rule and still have a reading order
  that makes no sense to a person.
- Our own 100 is our engine scored by the validator we chose. That is a fair
  measurement and a narrow one.

Anything stronger needs the corpus, not one file.

## Not in this report

No entity is named. No document contents, no hostnames, no credentials, no
account identifiers. Inputs, outputs and full measurements are in the private
corpus repository under `competitor-test/2026-09-17-remedocs/`.
