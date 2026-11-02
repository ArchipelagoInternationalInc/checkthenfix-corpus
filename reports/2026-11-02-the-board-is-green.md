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

## 4. Check 13 and 14

Pending at the time of writing. This report is filed **before** the single
`--final` run, because check 14 asks for a report pushed today, and running
`--final` first would have left 14 red on the only run allowed. Section 5 is
filled in after that run.

## 5. Live rows

Pending (see section 4).
