# Deployment verification after the PM's API unpause — still blocked

**Date:** 2026-08-20 06:12–06:14 UTC · `main` and `design-pass` identical at `94d3346`.

## DEPLOYMENT STATE — RED

> **The exposure is still open. There is no end time to record.**
> `checkthenfix.vercel.app` serves the superseded April 2026 / April 2027
> pairing as of this report.

## What was done

An empty commit was pushed to the production branch at **06:12:28Z**,
specifically so the outcome would be attributable to the unpause and nothing
else — the same instrument used twice before, per the delegation-log precedent.

| | |
|---|---|
| Push | 06:12:28Z, `main` |
| Deployment | `readyState: BLOCKED` |
| Project | `live: false` |

**Three unpause attempts now** — two by the Builder via the API, one by the PM —
each returning `paused: false`. `live` has never left `false`.

## The six-line verification, against the deployed URL

```
PASS  engine health          HTTP 200 veraPDF pinned 1.30 actual 1.30.2
PASS  auth fails closed      unauthenticated /check -> HTTP 404
PASS  canonical 42           May agenda scored 42 (expected 42)
FAIL  deployed /             HTTP 200, superseded dates present, corrected absent
FAIL  deployed /deadline     HTTP 404
FAIL  deployed /faq          HTTP 404
```

**The three that do not depend on Vercel are green.** The engine is healthy on
the pinned validator, refuses unauthenticated calls with a 404, and the canonical
May agenda still scores exactly 42 end to end.

### The two 404s are the most useful line in the investigation

`/deadline` and `/faq` were added to the repository on 2026-08-19, and the title
the alias serves — `Is your PDF accessible? — CheckThenFix` — was replaced the
same day.

> The alias is pinned to a build that **predates all of it**. That is independent
> confirmation from outside, rather than an inference from the API.

## Exposure window

| | |
|---|---|
| Began | When the pause stopped shielding the alias (owner's Resume attempt) |
| Ended | **Not ended** |
| Duration | **Over 24 hours and continuing** |

## Per the instruction, nothing else changed

`VERCEL_SUPPORT_EVIDENCE.md` now carries all three tests and leads with the
decisive one: **the unpause write returns `paused: false` and does not alter
`updatedAt`** — it reports success and modifies nothing.

## Morning items for the owner

1. **The support ticket** — evidence pack is ready and complete.
2. **Either of two dashboard clicks ends the exposure immediately**, without
   waiting for support:
   - Settings → Git → Production Branch → a branch name that does not exist.
   - Deployments → the current production deployment → Delete.

   Both leave the alias with nothing of ours on it. Neither is reachable from
   any tool available to the Builder.
