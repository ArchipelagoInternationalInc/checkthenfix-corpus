# A sign's quoted words are no longer a person: building described 3 of 3; chart still refused 3 of 3
## 2026-11-11 · Builder session (Studio Mac) · branch `builder/formal-wording` · part 3

Follows `2026-11-10-people-pictures-fixes-rerun.md`. Doppler `dev` only. No
payment. Nothing merged or deployed. No database change. This report gives
counts and fixed wording only, with no document text and no picture
descriptions.

## What changed (owner: "Yes, change it")

- **Quoted text and the people check:** the people check now ignores words
  inside quotation marks, such as a sign's "Visitor Center". This happens
  only when the second look on that same sentence answered clearly that no
  person appears.
  - Still refused: a person word outside the quotes, no second look, or a
    look about a different sentence.
- **The second look's answer:** it now records whether it gave a clear "no
  person".
  - Recorded only when its other checks of the answer pass first: the
    guesses list must be empty and the people field must be readable.
- **Tests:** 1019 passed. Lint and typecheck clean.
- **Review list:** the change is on the owner's `/review` list, checked on
  the preview at desktop width and at 390 px, with no console errors.

## Correction to part 2

In part 2, the chart's two retries were run through a harness that dropped the
list of words to remove, so those two results did not test the new retry.
Part 2 has been corrected. The harness is fixed, and everything below uses
the fixed harness.

## Re-run: building and chart, 3 runs each, real model (all compliant, 0 failed rules)

| Picture | Run | Draft length | Second look on draft | Retry | Outcome |
|---|---|---|---|---|---|
| Building | 1 | 111 | confirmed, 5 of 5, 0.99 | removed 2 names | described (78 characters), second look confirmed 4 of 4, 0.99 |
| Building | 2 | 120 | confirmed, 7 of 7, 0.99 | removed 2 names | described (95 characters), second look confirmed 6 of 6, 0.98 |
| Building | 3 | 112 | confirmed, 4 of 4, 0.99 | removed 2 names | described (71 characters), second look confirmed 4 of 4, 0.99 |
| Chart | 1 | 130 | found a guess | skipped | refused: length, 2 numbers not found, retry skipped |
| Chart | 2 | 127 | found a guess | skipped | refused: length, 2 numbers not found, retry skipped |
| Chart | 3 | 127 | found a guess | skipped | refused: length, 2 numbers not found, retry skipped |

- **Building: described 3 of 3.** Each final description was checked by
  hand on screen. None mentions a person, and none guesses a person's
  gender, age, race, feelings, intent or job.
- **Chart: refused 3 of 3.** The second look listed a guess every time. The
  fixer does not keep what it listed, so the cause is not known. The two
  numbers not found are years printed sideways on the axis.
- The photos of people were not re-run. This change only affects text in
  quotation marks, and only when the second look saw nobody.

## Open for the owner

1. Should we find out why the chart's second look always lists a guess? This
   would need a private diagnostic run that shows what it lists.
2. Should the reader also turn the image to read sideways axis labels? That
   means more reader calls per picture.
