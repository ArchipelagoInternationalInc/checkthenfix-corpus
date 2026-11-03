# No shortening retry after a failed second look; six of six sample pictures described
## 2026-11-03 · Builder session (Studio Mac) · branch `builder/paid-fix` · part 12

Follows part 11 (`2026-11-03-paid-fix-shortening-retry.md`). On one run there,
a long draft whose second look had already failed was still shortened,
because its recorded refusal was for length alone. The owner decided such
drafts get no retry.

Doppler `dev` (practice database) only. No payment. Nothing merged or
deployed. No database change. Counts and fixed wording only; no document text.

## What changed on the branch

- `ffa842c` (cloud builder) withheld the retry after a failed second look.
  It also withheld it for a long draft with a guess word, or one whose second
  look errored, and it did not record the skip in the plan note.
- `0d282db` (this session) brings it to the owner's rule and changes nothing
  else:
  - A long draft whose second look **ran and did not confirm it** gets no
    retry. The picture is refused as before. The note adds, in fixed
    wording, "shortening retry skipped: second look could not see N of M
    claims", or the second look's other fixed reason. An unrecognised reason
    is not copied; the note then says "the second look did not confirm it".
  - A second look that never ran (none attached, or one that errored) does
    not block the retry. A guess word in the long draft does not block it
    either, because no second look ran on it. The shorter text is filtered
    for guesses and gets its own second look, as before.
  - Length-only retry where the second look confirmed: unchanged. The 125
    limit, matching rule and retry count are unchanged (D-021, D-030).
  - DECISIONS.md and the launch checklist updated to match.
- Tests:
  - too long and failed the second look: no retry, refused with the skip
    note;
  - too long and passed the second look: still gets its one retry;
  - no second look, an errored second look, or a guess word in the long
    draft: still retried;
  - with the retry turned off, the note says nothing about skipping.
- Tests 884 passed. Lint and typecheck clean.

## Re-run: both samples, three runs each

All six fixed the document: veraPDF compliant, 0 failed rules, visual
difference 0.

| Run | First draft | Second look on it | Shortened to | Second look on the short text | Picture plan note |
|---|---|---|---|---|---|
| canonical 1 | 133 | confirmed, 6 of 6, 0.99 | 108 | confirmed, 6 of 6, 0.99 | described after 1 shortening retry |
| canonical 2 | 133 | confirmed, 6 of 6, 0.99 | 108 | confirmed, 6 of 6, 0.99 | described after 1 shortening retry |
| canonical 3 | 133 | confirmed, 6 of 6, 0.99 | 108 | confirmed, 6 of 6, 0.99 | described after 1 shortening retry |
| second agenda 1 | 133 | confirmed, 6 of 6, 0.99 | 108 | confirmed, 6 of 6, 0.99 | described after 1 shortening retry |
| second agenda 2 | 144 | confirmed, 6 of 6, 0.99 | 116 | confirmed, 6 of 6, 0.99 | described after 1 shortening retry |
| second agenda 3 | 139 | confirmed, 6 of 6, 0.99 | 114 | confirmed, 6 of 6, 0.99 | described after 1 shortening retry |

**Six of six pictures described**, each after one shortening and each
confirmed by the second look. No first draft failed its second look on these
runs, so the new skip did not occur here; it is covered by the tests.

## Practice database and servers

The fixing step ran directly, without the app or the engine servers. No rows
were written. This session started no servers.
