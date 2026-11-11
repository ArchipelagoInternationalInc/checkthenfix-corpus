# Slanted and sideways labels are now read: chart described 2 of 3 with its years confirmed
## 2026-11-11 · Builder session (Studio Mac) · branch `builder/formal-wording` · part 6

Follows `2026-11-11-stricter-retry-prompt.md`. The chart's axis years were
never found by the text reader. The owner approved trying a turned read ("Yes,
try it"). The same seven test PDFs were run 3 times each, with
`DESCRIBE_PEOPLE` unset. Doppler `dev` only. No payment. Nothing merged or
deployed. No database change. This report gives counts and fixed wording only,
with no document text and no picture descriptions.

## What the probe found first

- The test chart's axis years are printed on a slant of about 60 degrees.
- Read level, none of the 27 years were found. Turned 90 degrees, none were
  found either. Turned 60 degrees clockwise, all 27 were found; 45 degrees
  found 10.

## What changed

- When a description still has a name or number the level read did not find,
  the reader also reads the whole picture turned 60, 45 and 90 degrees in each
  direction (6 extra reads, sparse-text mode), and adds that text.
- A picture with nothing left to confirm gets no extra reads. On the chart,
  the six reads took about 2 seconds.
- Exact matching is unchanged. Reading more text very slightly raises the
  chance that a misread fragment matches a wrong number. This is the same
  trade as the earlier page-mode change.
- Tests: 1034 passed. Lint and typecheck clean.

## Re-run: 21 runs, all fixed (veraPDF compliant, 0 failed rules)

| Picture | Described | Retry | Second look |
|---|---|---|---|
| Desk ×3 | 0 of 3 | none | person 3 |
| Group ×3 | 0 of 3 | none | person 2, did not run 1 |
| Children ×3 | 0 of 3 | none | person 3 |
| Medical ×3 | 0 of 3 | none | person 3 |
| Officer ×3 | 0 of 3 | none | did not run 3 (guess filter refused first) |
| Chart ×3 | **2 of 3** (112, 120 chars) | length only, 3 | names/numbers only 5 of 5 looks |
| Building ×3 | 2 of 3 (73, 100 chars) | ran 2, removed 2 names each; skipped 1 | confirmed 4 of 4; 1 checked words not in the description |

- **Pictures of people: 15 of 15 refused, all with the people note first.**
- **Chart: 2 of 3 described, with both axis years kept and confirmed on the
  picture.** No names or numbers were left unconfirmed on any run. The third
  run's shortened text was still 127 characters, over the 125 limit, so it
  was refused.
- **Building: 2 of 3.** On one run the second look answered about words that
  are not in the description, so the retry was skipped. This is not related
  to the reader change; in parts 3 to 5 it was 3 of 3.

## Practice database and servers

No rows were written and no servers were started. Each run's output folder
was deleted when the run ended.
