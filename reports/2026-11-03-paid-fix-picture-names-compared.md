# Picture names and numbers compared: all printed in the picture, the text reader missed them
## 2026-11-03 · Builder session (Studio Mac) · branch `builder/paid-fix` · part 9

Follows part 8 (`2026-11-03-paid-fix-picture-notes-after-prompt.md`): after
the prompt change, both sample pictures were still refused because names or
numbers in the description were not found in the text read off the picture.
This pass compared each flagged item with the picture itself. The comparison,
with the words involved, is in the private corpus. This report carries counts
and conclusions only.

Doppler `dev` (practice database) only. No payment. Nothing merged or
deployed. No code changed.

## Method

- The two samples carry the same picture (a round seal), byte for byte.
- The fixing step ran three times per sample with the real AI model, six
  descriptions in all. For each run the description, the text the reader
  returned, and the check's verdict were saved privately.
- Each flagged item was checked against the picture and sorted into one of
  three buckets.

## Result

| | Flagged items |
|---|---|
| Total, across 6 runs | 10 |
| Distinct items | 2 (one word and one year) |
| (a) printed in the picture, reader missed it | **10** |
| (b) not in the picture: from the page or invented | **0** |
| (c) matching problem: spelling, case, punctuation, number format | **0** |

Every other name and number the AI used was found on the picture.

## Why the reader missed them

Both items sit on a small inner arc at the bottom of the seal. The ring
unwrapper flattens that arc and turns it upright correctly; in the flattened
strip the words are sharp and easy to read. But each strip is read in
"single line" mode, and this strip is not a single line: it also holds parts
of the central artwork, a dividing rule and the edge of the outer ring. In
that mode the reader returns noise for it. Read in block or sparse-text mode,
the same strips give both items, in every one of the strips tried.

## Proposals for the owner (not made)

1. **Also read each ring strip in sparse-text mode**, at least for the inner
   ring. Matching stays exact. Simulated on the six saved descriptions: the
   names-and-numbers refusal disappears in all six, while control
   descriptions with a wrong name or a wrong year are still refused. Cost:
   about 5 seconds more per seal for all strips, about 2 seconds for the
   inner ring alone.
2. **Leave room on length.** In all six runs the description was 129 to 135
   characters, just over the 125 limit, even with the limit in the prompt.
   Asking for about 100 to 110 characters, or one retry asking for a shorter
   version, would keep the 125 check as it is.
3. Not proposed: a looser match. None of the items was a matching problem.

With both 1 and 2, these descriptions would pass the first checks and reach
the second look, which has not yet decided on any real picture.
