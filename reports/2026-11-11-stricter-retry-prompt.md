# Stricter deletion-only retry prompt: chart described for the first time (1 of 3)
## 2026-11-11 · Builder session (Studio Mac) · branch `builder/formal-wording` · part 5

Follows `2026-11-11-chart-printed-guesses.md`. In that run, the chart's retry
answers were refused because the model added two words of its own while
deleting. The owner chose a stricter prompt over loosening the deletion guard.
The same seven test PDFs were run 3 times each, with `DESCRIBE_PEOPLE` unset.
Doppler `dev` only. No payment. Nothing merged or deployed. No database
change. This report gives counts and fixed wording only, with no document text
and no picture descriptions.

## What changed

- The retry prompt, when it asks the model to remove names or numbers not
  found on the image, now says:
  - every word in the answer must already be in the description;
  - nothing may be added, replaced or rephrased, not even a short joining
    phrase to fill the gap;
  - leftover words that only made sense with the removed ones go too.
- The deletion guard that checks the answer is unchanged.
- Tests: 1029 passed. Lint and typecheck clean.

## Probe

The same request, sent 5 times for the chart's draft and 5 times for a
building-style draft, gave 10 of 10 pure deletions. The previous prompt gave
0 of 4 on the chart.

## Re-run: 21 runs, all fixed (veraPDF compliant, 0 failed rules)

| Picture | Described | Retry | Second look |
|---|---|---|---|
| Desk ×3 | 0 of 3 | none | person 3 |
| Group ×3 | 0 of 3 | none | person 2, did not run 1 |
| Children ×3 | 0 of 3 | none | person 3 |
| Medical ×3 | 0 of 3 | none | person 3 |
| Officer ×3 | 0 of 3 | none | person 2, did not run 1 |
| Chart ×3 | **1 of 3** (99 chars) | ran 1 (pure deletion), skipped 2 | names/numbers only 1 (both looks), could not see 1 of 4 claims 2 |
| Building ×3 | 3 of 3 (94, 105, 105 chars) | ran 3, removed 2 names each | confirmed 6 of 6 |

- **Pictures of people: 15 of 15 refused, all with the people note first.**
- **Chart: described for the first time.** On one run the retry removed the
  two unreadable years, and the second look on the shorter text raised
  nothing beyond printed names and numbers. On the other two, the second look
  could not see one of the draft's four claims. That is a genuine objection,
  so the retry was skipped and the picture was left for the buyer.
- **Building: 3 of 3**, unchanged.

## Owner decision still open

Should the reader turn the image to read sideways axis labels? That means
more reader calls per picture.

## Practice database and servers

No rows were written and no servers were started. Each run's output folder
was deleted when the run ended.
