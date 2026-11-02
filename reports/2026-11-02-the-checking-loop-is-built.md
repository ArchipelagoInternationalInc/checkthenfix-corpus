# The checking loop is built. Half the board needs the studio machine.
## 2026-11-02 · Builder session (cloud)

The Definition of Done for stretch 1 now has its judge: `scripts/verify.sh`
runs checks 1 to 12 and prints PASS, FAIL or CANNOT-RUN for each. CANNOT-RUN
counts as red. Six checks are green from the cloud. Five cannot run there,
because they need the studio's secrets manager or direct access to the site.
One waits on the owner. Production was not touched.

---

## 0. Fresh state check

- `main` vs `design-pass`: main 2 ahead (the Nov 1 merge), design-pass 1 ahead
  (a handoff-only commit). No code differs.
- Production serves the Nov 1 merge commit.
- Work landed after Sep 25 (D-023 to D-025, the Oct 30 rollback, the Nov 1
  merge and promote), so GOAL.md section 6 is out of date. The Nov 1 handoff
  records the gap. This session recorded it and built only on a branch.

## 1. The board, from the cloud

| # | Check | Result |
|---|---|---|
| 1 | Identity | PASS |
| 2 | Lint | PASS |
| 3 | Types | PASS |
| 4 | Tests | PASS, 580 passed, 0 failed |
| 5 | Canonical document, local | PASS: 42 → 100, veraPDF 1.30.2 ua1, 0% pixels, 1,810 / 1,810 characters |
| 6 | No made-up scores | PASS: refuses with "validator did not run", no number |
| 7 | Delete guard refuses live | CANNOT-RUN: secrets manager not available |
| 8 | Deployed pages | CANNOT-RUN: secrets manager not available |
| 9 | Crawlers stay closed | CANNOT-RUN: the cloud network blocks the site |
| 10 | Stripe stays in test | CANNOT-RUN: secrets manager not available |
| 11 | No internal words | CANNOT-RUN: the cloud network blocks the site |
| 12 | Loop installed | FAIL: the permission lists and the second stop hook are proposed, not applied |

Read separately, not counted as PASS: the production robots file and its
no-index header were fetched through the hosting provider's own tool and are
correct; the three public pages built from the same code carry no internal
words; the delete guard refuses a production-shaped configuration (exit 78,
nothing opened).

## 2. Check 5 could not have passed as it was

The measuring script asked the engine to score each file. Since Sep 25 the
engine reads only the live project (D-022), so it was asked about files that
were not where it looks. Every answer was 404, and on Nov 1 it wrote seven
rows of 404 over the real results. It now scores locally with the same pinned
validator, as D-022 already prescribes for development. All seven pairs
reproduced the earlier scores exactly. Each row now also records the
validator's identity, the share of pixels changed and the characters before
and after, which is what check 5 asserts. A planted 99 fails.

## 3. Check 12 waits on the owner

The cloud session was not permitted to edit the file that sets its own
permissions. That is the right refusal. The full proposal is in the app repo
at `docs/CLAUDE_SETTINGS_PROPOSED.json`: allow, ask and deny lists, with
Supabase migrations, Vercel production, Stripe, Doppler secrets, force-push
and merge to main all on the ask list, and a second stop hook that refuses to
end a session while the board is red.

## 4. Gauntlet

Three blind critic rounds, each a fresh agent given only the bar and the repo,
working on copies with local stand-ins. All three failed, and each largest gap
was fixed:

1. Check 5 passed on stale numbers when the canonical pair could not be measured.
2. Check 12 passed on settings that would not guard anything.
3. Check 12 still passed when the guard was switched off from outside the hook.

The third set of fixes was proven by the builder against each case, not by a
fourth critic: the three-round budget is spent.

## 5. Live rows

None. Check 13 was not run.

## 6. What finishes the stretch

1. The owner applies the proposed settings (check 12).
2. On the studio machine, with the secrets manager signed in, the validator
   path set and the corpus linked: `scripts/verify.sh` (checks 7 to 11).
3. When 1 to 12 are green: `scripts/verify.sh --final` once (check 13, one
   anonymous row, token to the private corpus only), then this record (check 14).

Known looseness, reported rather than fixed: check 8 accepts empty FAQ and
deadline pages; check 9 accepts a robots file that blocks only one crawler
(DoD wording); the stop hook releases after three refusals so a session cannot
deadlock.
