# Exposure closed, root cause found, and a guard so the report cannot be skipped

**Date:** 2026-08-20 · Closes the thread left open by
`2026-08-20-deployment-verification-still-blocked.md`.

> **The exposure is CLOSED. End time 06:52:48Z.** The previous report in this
> repository ends with "still blocked, exposure still open." That was true when
> written and has been the public state of this record for hours after it
> stopped being true. Correcting that is the first purpose of this report.

## DEPLOYMENT STATE — GREEN

| | |
|---|---|
| Deployment | `readyState: READY`, `target: production` |
| Superseded April 2026 / April 2027 pairing | **0 occurrences** |
| Corrected dates | `April 26, 2027` and `April 26, 2028` both served |
| Previously-404 routes | now 200 |
| Node version | pinned 22.x, override warning cleared |

### Six-line verification against the live public alias

Run twice, at 06:52:48Z and again at 07:00:36Z. Identical both times:

```
PASS  engine health          HTTP 200   validator pinned 1.30, actual 1.30.2
PASS  auth fails closed      unauthenticated check -> HTTP 404
PASS  canonical 42           canonical agenda scored 42 (expected 42)
PASS  deployed /             HTTP 200, superseded dates: false, corrected: true
PASS  deployed /deadline     HTTP 200, superseded dates: false, corrected: true
PASS  deployed /faq          HTTP 200, superseded dates: false, corrected: true

TRIPLE GREEN
```

## Exposure window

| | |
|---|---|
| First **verified** public exposure | 03:42:32Z |
| Exposure ended | 06:52:48Z |
| **Verified duration** | **3h 10m** |

The true start is earlier and cannot be timestamped. It began when the owner's
Resume attempt stopped the pause from shielding the public alias, and that left
no record available to read. Recorded as unknown rather than rounded into a
number that would sound defensible and not be.

## Root cause: a stale git author identity

Every commit in the application repository was authored by a **personal GitHub
account**, taken from **global** git config with the repository-local identity
unset. That account is not a linked member of the deployment team.

## Enforcement point: the team-plan author rule

The hosting team plan **blocks any deployment whose commit author is not a
linked team member.** The refusal happens before the build starts — which is
why no build log ever existed to inspect, and why every diagnostic aimed at the
build was aimed at something that never ran.

Setting the repository-local identity to the linked team account produced a
successful build **on the first attempt, after 20-plus consecutive blocked
deployments.** Four subsequent deployments queued, built and went ready. The
mechanism is confirmed repeatable, not a coincidence.

The fix is repository-local only. Global config is the owner's machine identity;
changing it silently would re-attribute their work across every other repository
on that machine. That remains an owner decision, and until it is made, **every
other repository on that machine carries the same stale identity** and will
reproduce this on its first team deployment.

## `live: false` is a red herring — do not chase it

**At 07:00:36Z the project reported `live: false` while the deployment was
`READY`, targeted production, and the public alias served the correct site.**

Three separate investigations read this flag as a pause indicator. All three
were wrong. It did not gate anything at any point. A future session that sees
`live: false` should look at `readyState` and the actual HTTP response.

## The phantom pause/unpause writes, explained

Three unpause calls — two by the Builder, one by the PM — each returned
`paused: false` while the project's `updatedAt` never moved and nothing changed.

Not a platform bug. **Unpausing something that is not blocking cannot change
anything.** The project was never the thing refusing the deployments; the author
rule was. That finding was real and correctly measured, and it was evidence
*against* the pause theory, filed as evidence *for* it.

## What the investigation got wrong

| Signal | Read as | Actually meant |
|---|---|---|
| deployment `BLOCKED` | project paused | author not authorised |
| `buildingAt == ready == createdAt` | never entered a build | true, but from authorisation |
| error link → *troubleshoot project collaboration, team configuration* | boilerplate | **the answer, in the payload, every time** |
| `live: false` | the pause | never gated anything |
| unpause returns `paused: false`, nothing changes | platform defect | the project was not the blocker |

The error link said **collaboration** and **team configuration**. It was quoted
in three separate reports and read past three times, because a pause had already
been assumed and every new symptom was fitted to that assumption instead of
tested against it.

The lesson is not "read the error link." It is that **a committed hypothesis
silently reclassifies contradicting evidence as supporting evidence** — and the
strongest disconfirming signal available, the writes that changed nothing, was
the one filed most confidently under the wrong heading.

## The report-push miss, and the guard that ends it

This report is late, and the reason is worth recording as plainly as the outage.

**Three consecutive sessions ended believing a report had been filed here.** Two
pushed to the private application repository instead of this one. The third —
asked to confirm — checked the application repository's remotes, found the
document present, and reported the rule satisfied. The check read the wrong
remote and returned a confident pass.

A rule that depends on remembering it is not a rule. It has now been made
mechanical.

### The guard

A **`Stop` hook** in the application repository's Claude Code configuration,
`.claude/hooks/require-slice-report.sh`. On every session end it asks GitHub
directly whether any commit touching `reports/` in this repository has landed
since the session's first message. Session start is read from the transcript,
with a 12-hour fallback.

- **Report found** → the session ends normally.
- **No report** → the stop is refused, with instructions naming this repository
  explicitly, including that pushing to the application repository does not
  satisfy it.
- **Check could not run** (no `gh`, no network, bad repo) → also refused, with a
  message stating the status is **UNKNOWN** rather than claiming the report is
  missing.

It asks GitHub rather than the filesystem, because the failure mode was a local
belief that a push had happened. Server-side truth cannot be misremembered.

### Mutation-tested

Five simulated session-ends, before it was trusted:

| Case | Expected | Result |
|---|---|---|
| Session end, no report pushed | refuse | refused, correct instructions |
| Report exists in window | allow | allowed |
| `gh` absent from PATH | refuse as UNKNOWN | refused, UNKNOWN |
| Repository unreachable | refuse as UNKNOWN | **initially wrong — fixed** |
| Same session blocked repeatedly | bounded, no deadlock | released after 3, loudly |

The unreachable-repository case first reported "no report has been pushed" when
the truth was that the query had failed. It blocked either way, so it was
fail-closed and would have shipped unnoticed — but it would have told a future
session it had forgotten to push when it had not. That is precisely the wrong
diagnosis this incident is about, so it was fixed before shipping: the query's
exit status is now checked separately from its result.

The loop bound exists because a guard that can deadlock a session offline is a
worse failure than the one it prevents. After three refusals it releases, and
says in plain terms that the report is still not pushed.

## Standing risk, still open

- **Global git identity unchanged**, by deliberate choice. Owner's call.
- The `identity-preflight` skill is available in the session environment and is
  the correct instrument for this class. It is not automatic; it has to be run.
  The guard shipped here covers reports, not identity.

**No paid seat was required.** Adding the personal account to the team would
also have worked, at the cost of a seat, and would have left the defect in
place.
