# The board is green on the studio machine
## 2026-11-02 · Builder session (Studio Mac)

This session finished the work the cloud session could not. It ran on the
studio machine, which has the secrets manager, the pinned validator and the
private corpus. Checks 1 to 12 passed on the first run, so no code changed.
Production was not touched, and nothing was merged.

---

## 0. Fresh state check

```
origin/main...origin/design-pass   2  1
origin/main         b124484  Sun Nov 1 19:19:25 2026 -0500
origin/design-pass  2ecd9de  Sun Nov 1 19:25:49 2026 -0500
builder/dod-loop    5 commits ahead of design-pass, 0 behind (draft PR #1)
```

The state is the same as the cloud session found it. `design-pass` is one
handoff-only commit ahead of `main`, and no code differs. Branch
reconciliation is unchanged and **[NEEDS OWNER]**, because a merge to `main`
deploys production.

## 1. What this session changed

- **Settings applied (owner-approved).** `.claude/settings.json` is now the
  proposed file without its `_note` line: the allow, ask and deny lists, and
  the second stop hook that refuses to end a session while the board is red.
  This turned check 12 green.
- **The corpus is linked, not copied.** On this machine `corpus/docs` and
  `corpus/remediated` were real folders. They matched the private corpus
  except for one extra file there and one scratch test file here. They were
  moved aside, not deleted, and replaced with links into the private corpus
  clone. A link is not a `*.pdf`, so the corpus ignore file now names both
  folders. Without that, git offers to commit the links.
- **The validator path is set on this machine.** It lives in the local,
  untracked Claude settings, not in the committed file, because the path names
  a binary on this Mac only.

## 2. The board, checks 1 to 12

```
PASS   1  identity                   ArchipelagoInternationalInc
PASS   2  lint                       exit 0
PASS   3  types                      exit 0
PASS   4  tests                      Tests  590 passed (590)
PASS   5  canonical document, local  42 -> 100, veraPDF 1.30.2 ua1, 0% pixels, 1810/1810 chars
PASS   6  no made-up scores          refused: validator did not run; no number reported
PASS   7  delete guard refuses live  exit 78, Refusing, no connection opened
PASS   8  deployed pages             production: lines 1 to 6 PASS
PASS   9  crawlers stay closed       production: Disallow: / and x-robots-tag noindex
PASS  10  Stripe stays in test       prd key is a test key (value not printed)
PASS  11  no internal words          production /, /faq, /deadline: no matches
PASS  12  loop installed             settings and lists present; hook refuses on red, releases on green

VERIFY GREEN
```

Check 5 rewrote `corpus/BEFORE_AFTER.json` with the same values as the
committed file, so git shows no change.

## 3. Check 12: plant and remove

The planted fault raised the minimum test count to 99999, which no run can
meet.

```
planted:  FAIL   4  tests   exit 0: Tests  590 passed (590) (need >= 99999 passed, 0 failed)
          VERIFY RED — every line above that is not PASS counts
          exit 1
removed:  PASS   4  tests   Tests  590 passed (590)
          VERIFY GREEN
          exit 0
```

Every other check was PASS in both runs. The plant was reverted from git, not
by hand.

## 4. Checks 13 and 14: the single `--final` run

This report was first filed **before** the `--final` run. Check 14 asks for a
report pushed today, and running `--final` first would have left 14 red on
the only run allowed. This section was added after the run.

```
PASS  13  public path in production  7 of 7 PASS, public path scored 42 (token in .verify/13-final.log only)
PASS  14  record filed               report c69dd66 pushed today; handoff 7900ac3; GOAL.md and DoD tracked

VERIFY GREEN   (exit 0; checks 1 to 12 PASS again in the same run)
```

The deployment check's own lines, with the token removed:

```
PASS  engine health          HTTP 200 veraPDF pinned 1.30 actual 1.30.2
PASS  auth fails closed      unauthenticated /check -> HTTP 404
PASS  canonical 42 (local)   May agenda scored 42 (expected 42)
PASS  deployed /             HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /deadline     HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /faq          HTTP 200, superseded dates: false, corrected dates present: true
PASS  public path            scored 42 · token <private> (one anonymous row, self-removing)
```

**Stretch 1 is done by its own definition: checks 1 to 14 are green.** The
items still open are all marked [NEEDS OWNER]: the merge to `main`, the price,
and the GOAL.md section 1 and 5 questions.

## 5. Live rows

**One.** It was an anonymous upload of the canonical document through the
deployed site, and nothing was charged. It is removed by the ordinary
anonymous retention window, so nobody has to act. The output went to a file
before anything read it, so the token was captured on the first run and the
check was not re-run. The token is in the private corpus only.

## 6. Found after the record: the stop hook never ran its judge here

At the session's end the new stop hook refused, saying the board was red, and
printed an all-PASS summary. The summary was stale, left over from the
`--final` run. The hook had never started `verify.sh`: it ran the judge's
path without quotes, and the studio checkout's path contains a space.

Check 12 was green throughout. Its self-test proved that the hook refuses and
releases using stand-in judges with no space in their names, so it could not
see this.

Fixed on the branch:

- the judge is quoted;
- the stand-in judges now live under a path with a space, and the old hook
  fails that test (exit 2 on a green judge);
- a summary older than the run is no longer shown; the judge's own output is.

The real hook, run against the real `verify.sh` from the studio checkout:
exit 0, checks 1 to 12 PASS. No live row. Check 13 was not re-run.
