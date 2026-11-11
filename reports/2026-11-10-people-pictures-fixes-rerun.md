# Three picture-check fixes, re-run with the real model: people 15 of 15 refused with the people note; pictures without people still 0 of 6
## 2026-11-10 · Builder session (Studio Mac) · branch `builder/formal-wording` · part 2

Follows `2026-11-10-people-pictures-real-model-test.md`, using the same seven
test PDFs, three runs each, with `DESCRIBE_PEOPLE` unset. Doppler `dev` only.
No payment. Nothing merged or deployed. No database change. This report gives
counts and fixed wording only, with no document text and no picture
descriptions.

## What the investigation found (read-only, before any change)

1. **Chart, no shortening retry.** This was not a bug. A retry was allowed
   only for drafts refused for length alone, and the chart drafts were also
   refused by the names check. The first report gave a different reason, and
   it has been corrected.
2. **The text reader, not the rule, was the weak spot.**
   - Problem: a picture that is not a round seal was read with Tesseract
     `psm 7`, which treats the whole picture as one line of text. On the
     chart it returned nothing.
   - Page modes fix most of it: `psm 3` and `psm 11` read the chart's title
     and two of its values. Unconfirmed names and numbers dropped from 7 to 2.
   - The two left are years printed sideways on the axis.
   - The building's sign and the desk photo's patch are too small to read in
     any mode, even at three times the size.
3. **Desk, people note missing.** The people check ran only on text that had
   already passed the names check.

## What changed (owner-approved)

- **Reader:** page modes 3 and 11 for any picture that is not a seal. Exact
  matching is unchanged.
- **People check:** runs whatever else refused the text, and its reason is
  named first in the note. A person counts as detected if the text mentions
  one, or if the second look on the same text says one appears.
- **Retry:** the single retry may now also go to a draft refused for names or
  numbers not found on the image, with or without length. The model is asked
  to remove those words.
  - Pure deletion: no new word except a joining word, and none of the removed
    words left. Any other answer is refused, as before.
  - Only the first draft may lose names.
  - A failed second look, or a person in the picture, still means no retry.
- **Cost table:** counts the longer retry prompt.
- Tests: 1013 passed. Lint and typecheck clean.

## Re-run: 21 runs, all fixed (veraPDF compliant, 0 failed rules)

| Picture | Described | Note leads with | Retry | Second look |
|---|---|---|---|---|
| Desk ×3 | 0 of 3 | people reason (then 3 names not found) | none | person 1, guess 2 |
| Group ×3 | 0 of 3 | people reason | none | person 2, did not run 1 |
| Children ×3 | 0 of 3 | people reason | none | guess 3 |
| Medical ×3 | 0 of 3 | people reason | none | person 3 |
| Officer ×3 | 0 of 3 | people reason | none | guess 2, did not run 1 |
| Chart ×3 | 0 of 3 | length, 2 numbers not found | ran 2, skipped 1 | confirmed 2 (4 of 4, 0.99), guess 1 |
| Building ×3 | 0 of 3 | "it describes a person" (wrongly), 2 names not found | none | confirmed 3 (5 of 5, 7 of 7, 5 of 5; 0.99) |

- **Pictures of people: 15 of 15 refused, and all 15 notes lead with the
  people reason.** Before the fixes it was 12 of 15. No description of a
  person was kept.
- **Pictures without people: still 0 of 6.** There are two new findings,
  both waiting on an owner decision. Neither is changed here.
  - **Chart:**
    - The reader now finds the title and the values.
    - Two years printed sideways on the axis are still unread.
    - The retry ran twice. On a separate probe, both answers removed the
      years and added two words of their own, so the deletion guard refused
      them as designed. Those answers were not captured in the runs.
  - **Building:** the person-word check counts "Visitor" as a person, so
    every draft quoting the sign "Visitor Center" is refused as describing
    a person.
    - This also blocks the retry that would have removed those words.
    - Before the fixes, the names check refused these drafts first, so this
      was hidden. It would have refused the building as soon as the sign
      was read.
    - The second look, which is the model check for people, confirmed that
      no person appears.

## Owner decisions needed

1. Should the retry prompt be stricter, or should a deletion that adds a few
   plain words be allowed? Allowing it weakens the guard.
2. Should person words inside quoted sign text be ignored by the person-word
   check when the second look says no person appears? The other option is to
   keep refusing.
3. Should the reader turn the image to read sideways axis labels? That means
   more reader calls per picture.

## Practice database and servers

No rows were written and no servers were started. Each run's output folder was
deleted when the run ended.
