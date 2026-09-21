# Acrobat column filed, competitor report corrected
## 2026-09-20 · Builder session · short report; the substance is in the updated competitor report

Full detail: `2026-09-17-competitor-output-test.md`, now carrying all four
columns and three corrections.

## Filed

Twelve files to the private corpus, bytes identical: **six** documents × (output +
Acrobat action log). The brief said five. Two of the six are files removed from
the corpus in August as not government documents; their numbers are reported
separately and excluded from corpus figures.

**Acrobat's accessibility checker reports could not be collected.** The guided
action writes them as HTML into a system temporary directory. They were gone
before this session looked, and nothing matching them is in the owner's Documents
folder or anywhere on disk. The action logs survive and are filed; they record
which steps ran, not which rules passed. Next run: save the report from Acrobat's
checker panel before closing the file.

## The Acrobat column

**85** on our veraPDF 1.30.2 / ua1. It is the only one of the three engines that
**changed the page** — 0.608% of pixels — and the only one that **added a font
that is not embedded**, so a file that was fully portable before no longer is.
Three failures: no PDF/UA identifier, untagged content, the font.

Five of the PM's six preview findings confirmed exactly: 34 elements, no headings
at all, no PDF/UA identifier, the title still the Word filename from a different
month, and the seal treated as decoration. **One corrected: the character count
did not rise.** Every extraction method gives byte-identical counts for the
original and the output. The OCR ran and changed no text.

Across six documents, validator only: **85, 71, 71, 28** for corpus documents
(from 42, 42, 38, 14 before), and 47 and 71 for the two non-corpus files.
**Acrobat improved every document and finished none.** All six are missing the
PDF/UA identifier, all six have untagged content, all six have a font problem —
one tool behaving the same way six times.

## Three corrections to the competitor report

**The headline was missing.** Their own HTML report, on the file they score
100/100, records "Correct reading order: False" and "1 of 1 pages flagged for
review". The score and the report contradict each other, and the score is what
the customer sees. Now stated at the top of that report.

**Their seal description is confidently wrong, and I had not checked it.** The
previous revision called it the one place their output beats ours and reasoned
about its length and its AI origin without asking whether it was true. Checked
against the image — which was available the whole time — it contains **five
factual errors, including the entity's own name, misspelled in both places it
appears**. Full comparison in the private corpus; nothing of it published. This
changes the comparison rather than reversing it: ours says nothing, theirs says
the wrong thing confidently, and a confident error cannot be noticed by the
reader who depends on it.

**The scoring-method note is confirmed**, verbatim, from the owner's dashboard
captures. Previously recorded as not captured. Their pre-20-July scores are, by
their own statement, AI estimates not comparable with compliance scores.

## One guard added

The measuring scripts overwrote their results files with an empty result when
they measured nothing — on 2026-09-20 that turned 1,160 bytes into 3 and 2,900
into 3. Both were in version control and were restored; the survey's mapping file
is not, and the same run would have destroyed it. They now refuse, say so, and
leave the previous file alone; the survey's mapping throws rather than warns. The
test reproduces the incident and requires the file to be byte-for-byte unchanged.
Mutation-tested. **468 tests pass**, lint and types clean.

## Housekeeping

Section 7 of the competitor report is marked **superseded** by
`2026-09-20-database-safety-gap.md`, so the two no longer contradict each other.

## What I got wrong, and what it has in common with last time

Last session's correction was an instrument reporting a confident wrong number.
This session's is the same shape in a different place: I reproduced a competitor's
description, reasoned about it at length, and never checked it against the image
sitting in the corpus. Both times the error was not a missing capability but an
unasked question, and both times the wrong answer looked finished.

## Not in this report

No entity name, nothing of the seal's contents, no account identifiers, no
hostnames, no document contents. Audited the public repository for the entity's
real name and its misspelling: **zero hits in the working tree and zero across
all history.**
