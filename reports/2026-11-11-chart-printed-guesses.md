# Printed names and numbers no longer count as guesses: chart retry now runs, deletion guard still refuses
## 2026-11-11 · Builder session (Studio Mac) · branch `builder/formal-wording` · part 4

Follows `2026-11-11-chart-second-look-diagnostic.md`. That run found that the
second look listed the chart's printed names and numbers as guesses. The owner
approved a change ("Yes, make it"). The same seven test PDFs were run 3 times
each, with `DESCRIBE_PEOPLE` unset. Doppler `dev` only. No payment. Nothing
merged or deployed. No database change. This report gives counts and fixed
wording only, with no document text and no picture descriptions.

## What changed

- A second-look guess no longer refuses by itself when every guess is a name
  or number that is either:
  - found on the image by the text reader (same exact matching as the names
    check), or
  - already refused by the names check.
- In that case the one deletion-only retry may run. The shorter text gets its
  own second look.
- A guess that is a person word or a judgment, and any other objection by the
  second look, still refuses.
- Guesses are held in memory only and never appear in a note.
- Tests: 1028 passed. Lint and typecheck clean.

## Re-run: 21 runs, all fixed (veraPDF compliant, 0 failed rules)

| Picture | Described | Note leads with | Retry | Second look |
|---|---|---|---|---|
| Desk ×3 | 0 of 3 | people reason | none | person 3 |
| Group ×3 | 0 of 3 | people reason | none | person 3 |
| Children ×3 | 0 of 3 | people reason | none | person 3 |
| Medical ×3 | 0 of 3 | people reason | none | person 3 |
| Officer ×3 | 0 of 3 | people reason | none | person 2, did not run 1 |
| Chart ×3 | 0 of 3 | length, 2 numbers not found | ran 2 (answer refused), skipped 1 | names/numbers only 2, could not see 1 of 4 claims 1 |
| Building ×3 | 3 of 3 (74, 87, 88 chars) | — | ran 3, removed 2 names each | confirmed 6 of 6 |

- **Pictures of people: 15 of 15 refused, all with the people note first.** No
  description of a person was kept.
- **Building: 3 of 3 described**, unchanged from part 3.
- **Chart: still 0 of 3.** The guesses no longer block it. On 2 runs the
  retry ran. Each time the model was asked to delete two years it could not
  prove, but it also added two plain words, so the deletion guard refused the
  answer. On a separate probe with the same request, both answers did the
  same. On the third run the second look could not see one of the four
  claims, a genuine objection, so the retry was skipped.

## Owner decisions still open

1. Should a deletion be allowed to add a couple of plain words, or should the
   retry prompt be stricter? Allowing it weakens the guard.
2. Should the reader turn the image to read sideways axis labels? That means
   more reader calls per picture.

## Practice database and servers

No rows were written and no servers were started. Each run's output folder
was deleted when the run ended.
