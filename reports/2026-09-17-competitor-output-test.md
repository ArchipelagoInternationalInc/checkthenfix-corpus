# Competitor output test — COMPLETE, ALL FOUR COLUMNS
## 2026-09-17 opened · **2026-09-20 completed and corrected** · investigation only; nothing about the product changed

All four columns are filled. The Acrobat column was added on 2026-09-20 from
outputs the owner produced with Acrobat's "Make Accessible" guided action.

**This revision carries three corrections, one of them to a claim this report
made in our own favour and one to a claim it made in the competitor's.** They are
marked **CORRECTION** where they appear. Findings only, no disparagement.

## The headline finding: a perfect score and a self-reported wrong reading order, in the same file

The competitor's own HTML report, on the same file it scores **100/100**, says:

> "Correct reading order: **False**"

and

> "Programmatic reading order check: reading_order_valid=false (1 of 1 pages
> flagged for review)."

Its own remediation notes repeat it. Under a criterion titled "Reading order may
not be logical due to lack of tagging", marked **RESOLVED**, the evidence given
is the failing check itself.

So the tool flagged the only page of the document for human review, recorded that
the reading order is not correct, and reported a perfect score anyway. **The
score and the report contradict each other, and the score is the part the
customer sees.**

Reading order is not a minor criterion. It is the order in which a screen reader
speaks the page — the single thing tagging exists to get right. A buyer who reads
only the number is told the document is finished. A buyer who opens the report is
told one of one pages needs a human.

This report's earlier revision quoted their 100/100 claim and noted our validator
agreed. **Both statements were true and both were incomplete**: our validator
agrees the file satisfies the machine-checkable rules, and their own tooling says
the reading order is wrong. Machine-checkable conformance and a correct reading
order are different questions, and this file is a clean demonstration that a
document can pass the first while failing the second.

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

## 3. Dashboard scoring-method note — **CORRECTION: confirmed**

Previously recorded here as "not captured". It is captured. Two dashboard
screens the owner saved carry it verbatim:

> "Scoring method changed on Jul 20, 2026 — earlier scores are AI estimates and
> are not directly comparable to newer compliance scores."

This matters for how their published numbers are read. Any score of theirs from
before 20 July 2026 is, by their own statement, an AI estimate rather than a
compliance measurement, and not comparable with anything measured since. It has
no bearing on the score in this report — that one was produced on 2026-09-20,
well after the change — but it does mean their older public figures and this
one are different kinds of number.

## 4. The table — all four columns

Canonical document, single page. Same validator for every column: veraPDF
1.30.2, profile ua1. Same pixel and character instrument for every column.

| | our before | our after | their after | **Acrobat after** |
|---|---|---|---|---|
| **Score (our validator)** | **42** | **100** | **100** | **85** |
| Tagged | no | yes | yes | yes |
| Structure elements | 0 | **123** | **489** | **34** |
| Headings | none | 1 H1, 10 H2 | 1 H1, 4 H2, 2 H3 | **none at all** |
| Element mix | — | 1 Document, 1 H1, 10 H2, 111 P | 1 Document, 7 Sect, 1 H1, 4 H2, 2 H3, 2 P, 1 Figure, 1 Table, 47 TR, 1 TH, 422 TD | 1 Artifact, 15 P, 2 StyleSpan, 1 L, 5 LI, 5 Lbl, 5 LBody |
| Table structure | — | none | 47 × 9, 423 cells, 336 empty | none |
| The seal | untagged | artifact, no description | Figure + 424-character description | artifact in effect, no description |
| Declares a standard (PDF/UA id) | no | yes | yes | **no** |
| Document title | Word filename | corrected | corrected | **still the Word filename, and from a different month than the document** |
| Fonts all embedded | yes | yes | yes | **no — one added, not embedded** |
| Extracted text | 1,810 chars | 1,810 — identical | 1,810 — identical | **1,810 — identical** |
| Page count | 1 | 1 | 1 | 1 |
| Visual change vs original | baseline | **0.0% of pixels** | **0.0% of pixels** | **0.608% of pixels** |
| Their own claim | — | — | **100/100** | — (no score claimed) |

Three engines, three answers: 100, 100, 85. Ours and the competitor's are
pixel-identical to the original; **Acrobat is the only one that changed what the
page looks like.**

### What our checker found wrong with the Acrobat file

Three failures, and each is a real defect rather than a technicality:

- **`meta-ua-id`** — the file never says which standard it meets. A PDF/UA
  identifier is how a consuming tool knows to trust the tagging at all.
- **`struct-content-tagged`** — not all content is tagged or marked as
  decoration.
- **`text-fonts`** — **a font was added that is not embedded.** The original had
  six fonts, all embedded; the output has those six plus an unembedded Type 1.
  A file that was fully portable before is no longer, and that is almost
  certainly what the 0.608% of changed pixels is.

### The PM's preview, checked point by point

| PM's finding | Verdict |
|---|---|
| 34 structure elements | **confirmed** — exactly 34 |
| No headings at all | **confirmed** — zero H1–H6, on a document whose first line is its title |
| No PDF/UA identifier in the metadata | **confirmed** |
| Title still the Word file's name, from a different month | **confirmed** — in both the document information and the XMP, naming April on a May document |
| Text recognition run on a document that already had text | **confirmed** — the action log shows OCR ran and succeeded |
| …with the extracted count rising 1,810 → 2,543 | **CORRECTED — it did not rise.** Every extraction method gives byte-identical counts for the original and the output. The OCR ran and changed no text |
| Seal treated as decoration | **confirmed**, with a wrinkle: the image sits inside a `Figure` marked-content region whose id is claimed by a structure element of type `Artifact`, and no alt text exists anywhere. A reader skips it, which is decoration in effect, but the file says two different things about the same image |

Five of six confirmed exactly; one corrected. The action log also records
"Alternate text set on 0 images", which is the same fact from Acrobat's side.

### Acrobat across six documents — the first look beyond one file

Validator only, same version and profile. Before-scores for the four that are
still corpus documents:

| Document | before | after Acrobat |
|---|---|---|
| the canonical agenda | 42 | **85** |
| a council agenda package | 42 | **71** |
| a set of approved minutes | 38 | **71** |
| a manager's recommended budget (507 pages) | 14 | **28** |
| *(a professional-association document, no longer in the corpus)* | — | 47 |
| *(a browser print capture, no longer in the corpus)* | — | 71 |

**Acrobat improved every document and finished none of them.** The gains are
real — +43, +29, +33, +14 — and the ceiling is real too.

**Three failures are universal: every one of the six is missing the PDF/UA
identifier, has untagged content, and has a font problem.** That is not six
documents behaving differently; it is one tool behaving the same way six times.

Six documents were run rather than the five in the brief, and two of them are
files removed from the corpus in August as not government documents. Their
numbers are shown separately and excluded from the corpus figures.

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

## 5a. **CORRECTION** — their seal description is confidently wrong

The previous revision called their description "the one difference that matters"
and treated it as the place their output beats ours, with caveats about length
and AI generation. **It did not check whether the description was true.** It has
now been checked against the image, which was available the whole time.

**The description contains five factual errors**, including the entity's own name
— misspelled in both places it appears — along with two wrong dates, a
misidentified central device, and a wrong motto. The peripheral ornaments it
describes are correct; the facts that identify the entity are not. The full
comparison is in the private corpus; nothing of the seal's contents or the
entity's name appears here.

The description is fluent, specific and confident. Nothing in its wording marks
the invented parts as uncertain, and their interface offers "Accept AI
suggestion" as a one-click action.

This changes the comparison rather than reversing it:

- **Ours says nothing about the seal.** A reader learns nothing. That is a gap.
- **Theirs says the wrong thing in a confident voice.** A reader is told the
  entity's name, the year it was chartered, what is at the centre of its seal and
  what its motto means — and four of those are wrong.

A gap can be noticed and filled. A confident error cannot be noticed by the
reader who depends on it, and it is published under the entity's own name. On
this document ours is the safer failure, and neither is good: the right answer is
a description that is checked.

It also reframes the earlier comparison of element counts. Their 489 elements
against our 123 looked like thoroughness. The extra structure includes a
423-cell table that is four-fifths empty and a description that is mostly wrong.
More tagging is not more accessibility.

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

## 6. Acrobat column — **now filled**

On 2026-09-17 `acrobat-output/` did not exist and, per the brief, nothing was
created. The owner produced the outputs on 2026-09-20; they are filed in the
private corpus with Acrobat's action logs, and the column above is measured from
them.

**Acrobat's own accessibility checker reports could not be collected.** The
guided action writes each one as HTML into a system temporary directory; they
were gone before this session looked, and nothing matching them exists in the
owner's Documents folder or anywhere else on disk. The action logs survive and
are filed, but they record which steps ran, not which rules passed. To keep them
next time, save the report from Acrobat's checker panel before closing the file.

## 7. Separate line of evidence — which database our scripts run against — **SUPERSEDED**

> **Superseded by `2026-09-20-database-safety-gap.md`.** Everything below was
> accurate when written and is no longer the current state: the refusal now
> exists, development and staging point at a throwaway, and the sweep was
> completed. Read the 20 September report instead. The text is kept because the
> reports are a record, not a description of today.

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

All four columns are now filled, and one page of one document still supports very
little. The Acrobat column is the exception in one respect — it has six documents
behind it, which is why the three failures common to all six are stated as a
pattern while everything else here is not:

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
- **A score is not a reading order.** The clearest lesson in this report is at
  the top: a file can satisfy every machine-checkable rule, score 100 from two
  independent validators, and still have a reading order its own producer flags
  as wrong. Our 100 is subject to exactly the same limit. We have not checked our
  own file's reading order against a human's judgement either.

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
