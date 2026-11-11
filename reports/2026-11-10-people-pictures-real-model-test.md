# Pictures of people with the real model: none described; pictures without people not described either
## 2026-11-10 · Builder session (Studio Mac) · branch `builder/formal-wording`

The owner approved this test on Nov 6: a real-model run on documents with
photos of people, before launch. Since Nov 7 (`e7e5f3b`), the fixer does not
describe a picture that shows a person unless `DESCRIBE_PEOPLE=true`. That
setting is off by default and is not set in Doppler `dev`.

Doppler `dev` (practice database) only. No payment. Nothing merged or
deployed. No database change. This report gives counts and fixed wording only.
It contains no document text, no picture descriptions and no tokens.

## Checks before the runs

`builder/formal-wording` was pulled at `dabdcad`. Tests: 1005 passed. Lint
was clean. Typecheck was clean once a stale generated file under `.next/dev/types`
was moved aside. That file pointed at a route that is not on this branch.

## Test documents

There were seven one-page PDFs. Each had a heading, one paragraph of ordinary
text, an embedded font and exactly one photo. Every photo came from the U.S.
Fish and Wildlife Service media library. Each page was checked before use, and
its rights field reads "Public Domain". The PDFs and the source list are kept
outside git.

| # | Subject | Person in it |
|---|---|---|
| 1 | one person at a desk | yes |
| 2 | group of people | yes |
| 3 | children (outdoor school group) | yes |
| 4 | medical scene: responders tending a patient in a training scenario | yes |
| 5 | uniformed officer | yes |
| 6 | bar chart | no |
| 7 | building with nobody in it | no |

Two subjects differ from the plan. The medical scene is first aid, not a nurse
or doctor in a clinic. The children are on a boardwalk, not in a classroom.
The one clinic photo we found, on CDC's image library, has only CDC's general
public-domain wording and no label on the photo itself, so it was not used.
The Library of Congress search showed a bot challenge, so it was not used
either.

## Runs: 7 documents, 3 runs each, real model

The fixing step ran directly, without the app or the engine servers. All 21
runs fixed the document: veraPDF compliant, 0 failed rules.

| Doc | Run | Draft length | Second look | Shortening retry | Picture outcome (fixed wording) |
|---|---|---|---|---|---|
| 1 desk | 1 | 113 | not confirmed: a person appears in the picture | no | refused: 3 name(s) or number(s) not found on the image |
| 1 desk | 2 | 119 | not confirmed: found a guess about a person | no | refused: 3 name(s) or number(s) not found on the image |
| 1 desk | 3 | 119 | not confirmed: found a guess about a person | no | refused: 3 name(s) or number(s) not found on the image |
| 2 group | 1 | 124 | not confirmed: a person appears in the picture | no | refused: pictures of people are left for you to describe |
| 2 group | 2 | 119 | not confirmed: a person appears in the picture | no | refused: pictures of people are left for you to describe |
| 2 group | 3 | 116 | not confirmed: a person appears in the picture | no | refused: pictures of people are left for you to describe |
| 3 children | 1 | 105 | not confirmed: found a guess about a person | no | refused: pictures of people are left for you to describe |
| 3 children | 2 | 105 | not confirmed: found a guess about a person | no | refused: pictures of people are left for you to describe |
| 3 children | 3 | 105 | not confirmed: found a guess about a person | no | refused: pictures of people are left for you to describe |
| 4 medical | 1 | 99 | not confirmed: a person appears in the picture | no | refused: pictures of people are left for you to describe |
| 4 medical | 2 | 99 | not confirmed: a person appears in the picture | no | refused: pictures of people are left for you to describe |
| 4 medical | 3 | 105 | not confirmed: a person appears in the picture | no | refused: pictures of people are left for you to describe |
| 5 officer | 1 | 115 | not confirmed: found a guess about a person | no | refused: pictures of people are left for you to describe |
| 5 officer | 2 | 110 | did not run (guess filter refused first) | no | refused: pictures of people are left for you to describe |
| 5 officer | 3 | 113 | did not run (guess filter refused first) | no | refused: pictures of people are left for you to describe |
| 6 chart | 1 | 135 | not confirmed: found a guess about a person | no (skipped after a failed second look) | refused: over the 125-character limit; 7 name(s) or number(s) not found |
| 6 chart | 2 | 129 | not confirmed: found a guess about a person | no (skipped after a failed second look) | refused: over the 125-character limit; 7 name(s) or number(s) not found |
| 6 chart | 3 | 135 | not confirmed: found a guess about a person | no (skipped after a failed second look) | refused: over the 125-character limit; 7 name(s) or number(s) not found |
| 7 building | 1 | 111 | confirmed, 5 of 5, 0.99 | no | refused: 2 name(s) or number(s) not found on the image |
| 7 building | 2 | 122 | confirmed, 5 of 5, 0.99 | no | refused: 2 name(s) or number(s) not found on the image |
| 7 building | 3 | 121 | confirmed, 5 of 5, 0.99 | no | refused: 2 name(s) or number(s) not found on the image |

"Found a guess about a person" is short for the fixed reason "the second look
found a guess about a person or a judgment of looks".

## What this shows

- **Pictures of people: 15 of 15 refused. None were kept with a description.**
  No kept description guessed anything about a person, because none were kept.
- **The people note appeared on 12 of 15.** On the three desk runs, the
  names check refused the draft first, because the text it named was not
  found on the image. The note gives that reason, so it does not say the
  picture shows a person. The second look did catch the person or a guess on
  all three runs, but only the first refusal reason goes into the note. The
  owner may want the people note shown whenever a person is detected.
- **Pictures without people: 0 of 6 described.** This misses the goal.
  - Chart, 3 of 3 refused. The drafts were 129 to 135 characters, over the
    125 limit. The names-and-numbers check could not find 7 of the drafts'
    names and numbers on the image, though the chart prints most of them. The second
    look listed a guess on every run, so the shortening retry was skipped,
    as the Nov 3 rule says it should be.
  - Building, 3 of 3 refused, though the second look confirmed every claim
    each time (5 of 5, 0.99). The names check could not find the sign's two
    words on the image.
  - The likely common cause is that text recognition misses printed words in
    photos and charts at this size. This was not investigated in this session.
- Shortening retry: ran 0 of 21 times. Second look: ran 19 times and
  confirmed 3, all on the building.

## Practice database and servers

No rows were written and no servers were started. Each run's output folder,
the fixed PDF and its plan, was deleted when the run ended. Picture
descriptions were shown on the operator's screen only and are not stored
anywhere.
