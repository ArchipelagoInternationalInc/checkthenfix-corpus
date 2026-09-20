# Competitor output test — COMPLETE
## 2026-09-17 opened · **2026-09-20 completed** · investigation only; nothing about the product changed

The owner ran the competitor's one free remediation on 2026-09-20 and supplied
three files. All four measurements are now in. The Acrobat column remains absent.

Findings only, no disparagement. Both outputs are good; they differ in one way
that matters and several that do not.

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

**Still not captured, as of 2026-09-20.** The three files the owner supplied are
the remediated PDF, its HTML report, and the printed job page. Searched all three
for that wording and for the date: **not present in any of them.** So this remains
unconfirmed rather than confirmed or denied. If the note is real it substantially
weakens any comparison against their older published numbers, which is why it is
still worth a screenshot of the dashboard itself.

## 4. The table — three of four columns

Canonical document, single page. Same validator for every column: veraPDF
1.30.2, profile ua1 — the version and profile named on our own reports. Same
pixel and character instrument for every column.

| | our before | our after | their after | Acrobat |
|---|---|---|---|---|
| **Score (our validator)** | **42** | **100** | **100** | **absent** |
| Tagged | no | yes | yes | — |
| Structure tree | absent | present | present | — |
| Structure elements | 0 | **123** | **489** | — |
| Element mix | — | 1 Document, 1 H1, 10 H2, 111 P | 1 Document, 7 Sect, 1 H1, 4 H2, 2 H3, 2 P, 1 Figure, 1 Table, 47 TR, 1 TH, 422 TD | — |
| Table structure | — | none | 47 rows × 9 columns, 423 cells | — |
| — of which empty | — | — | **336 (79.4%)** | — |
| The seal | untagged | **artifact**, no description | **Figure**, 424-character description | — |
| Extracted text | 1,810 chars | **1,810 — identical** | **1,810 — identical** | — |
| Page count | 1 | 1 — unchanged | 1 — unchanged | — |
| Visual change vs original | baseline | **0.0% of pixels** | **0.0% of pixels** | — |
| **Their own claim** | — | — | **100/100** — "70/70 machine rules, 30/30 document facts", no failures, no deductions | — |
| What our checker says about their file | — | — | **100** — see the row above; the two agree | — |

Neither engine moved a single pixel and neither lost a character. On the two
things a public entity would worry about first — does my document still look the
same, and is any text gone — the outputs are indistinguishable.

### The one difference that matters

Their file tags the seal as a **Figure with a 424-character description**. Ours
marks it as an **artifact with no description at all**.

For a reader using a screen reader, ours says nothing is there. Theirs reads out
a paragraph. That is a real difference in what the document does for a person,
and it is the only one in the table that a user would notice.

It is not simply a point in their favour. Their description is long, it is
AI-generated (their own interface labels it "AI viewed this image", offers
"Accept AI suggestion", and provides a "Mark decorative" button), and a
letterhead seal repeated on every page of a long document becomes a paragraph of
noise repeated on every page. PDF/UA permits artifacting genuinely decorative
content, and both engines produce a conformant file. Which is better depends on
whether that seal carries meaning a reader needs — a question about the document,
not about either engine.

### Their table, and why the empty cells are worth noting

Their 489 elements against our 123 is mostly one structure: a **47 × 9 table**,
consistent width on every row, 423 cells, of which **336 are completely empty**
— no children at all. 87 cells carry text; 1 cell is a header.

A table is the right tag for tabular data and the wrong tag for page layout. Four
hundred and twenty-three cells on a one-page agenda whose text amounts to 1,810
characters, with four in five of them empty and exactly one header cell, is the
shape of a **layout grid** rather than a data table. A screen-reader user
navigating it by table commands would traverse a 47-row grid that is mostly
nothing. Our output has no table at all, which on this document is the simpler
answer.

Stated as a finding, not a criticism: both files satisfy the validator. The
validator does not ask whether a table is really a table.

### Counts checked independently

The PM's figures were 489 elements, a 47 × 9 table, and 336 empty cells. All
three reproduce exactly on our instrument. Getting there required fixing that
instrument twice — see the correction at the end, which matters more than the
agreement does.

## 5. How the missing half got done

On 2026-09-17 this stopped because completing it required logging into their app,
which means entering the account password — something the Builder does not do on
any site, for any reason, including when the owner supplies it and authorises the
use. That has not changed.

The owner ran the single free remediation on 2026-09-20 and supplied the three
output files. Scoring them needed no account, so the rest completed unattended.

**One scan was used. No second account was created. Nothing was purchased.**

Their terms make this unambiguous: the remediated PDFs they generate from your
documents are "yours to use however you like."

## 5b. Our own engine and the seal — a decision, but an unexamined one

Asked directly: **our engine marked the seal as an artifact.** Not untagged —
the content stream carries explicit `/Artifact` marked-content, which is why the
file conforms at all. No `Figure` element, no alt text anywhere in the file.

**Was it a decision or an omission? Both, in different places, and the honest
answer needs the distinction.**

The *policy* is a deliberate, documented decision, and a careful one. The engine
has three branches for an image:

1. alt text supplied → tag it as a `Figure` and attach that text;
2. marked decorative → artifact, no description;
3. **neither** — not decorative, and no alt text → **the engine declines the
   whole document rather than guess.** Its own comment for that branch is
   "refusing to bluff."

That third branch is the good part, and it is why our scores can be trusted: the
engine will not invent a description.

The *application to this document* was not a decision about this image. Branch 2
fired from a **default** — `decorative` defaults to true, commented in the code
as "conservative default for the simple class = decorative artifact
(logos/letterhead)". And the judgment record for this document contains items for
language, reading order and headings, and **no alt-text item at all**: the pass
that looks at images never ran here. Nothing examined the seal. It was artifacted
because that is what happens to an image nobody asked about.

For a letterhead seal that default is probably right, and the result is
conformant. But "probably right by default" is not the same as "decided", and the
competitor's file is the reason the difference is now visible: they ran their
image pass, we did not run ours.

**The rule, stated plainly:** an image gets a description if one is supplied, an
artifact if it is marked decorative, and the whole document is declined if it is
neither. Silence from the judgment layer is currently read as "decorative". That
last clause is the one worth the owner's attention — it is a default standing in
for a judgement, and on a document where the image carries the entity's identity
it is the difference between a reader being told something and being told nothing.

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

## Correction — the instrument was wrong twice, and only their file showed it

The structure instrument was described on 2026-09-17 as "written, committed, and
proven against our own two columns". That last clause turned out to be the whole
problem. Run against a file we did not produce, it was wrong twice:

1. It read the top of the structure tree and gave up unless it found a single
   dictionary. Our engine writes one; theirs writes a **list**. The instrument
   reported **0 elements for a file containing 489** — and reported it calmly,
   with no error, alongside a correct "this file is tagged".
2. Its test for whether a cell holds anything recognised only the short form of a
   content reference. Theirs uses the long form. That made **87 cells holding real
   text look empty**, which would have put the empty-cell count at 423 instead of
   336 — and would have had this report contradicting the PM's count, with the
   error entirely mine.

Both are fixed, and re-running the corrected instrument on our own file returns
123 elements, unchanged, so nothing in our column ever moved.

The lesson is not "check the parser". It is that **an instrument validated only
against your own output is validated against your own assumptions.** The first
bug produced a confident, plausible, completely wrong number. Had the PM not
counted independently, 0 elements is exactly the kind of result that gets
believed and published.

## Not in this report

No entity is named — and this document's seal carries one, in the text their
description reproduces, so that description's contents are deliberately absent
and only its length is given. No document contents, no seal contents, no
hostnames, no credentials, no account identifiers, and no job references. Inputs,
outputs and full measurements are in the private corpus repository under
`competitor-test/2026-09-17-remedocs/`.
