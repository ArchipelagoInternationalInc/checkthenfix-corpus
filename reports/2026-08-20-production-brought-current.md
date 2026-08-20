# Production brought current — merge, Node pin, and what still needs the owner

**Date:** 2026-08-20 · `main` and `design-pass` are identical at `abc569b`.

---

## Deployment state

> **Project: PAUSED (`live: false`). Every deployment BLOCKED. Nothing is
> viewable on any public URL.**

This is the confirmed cause of the round-6.6 finding. The Vercel project API
reports `live: false`, and blocked deployments carry `buildingAt == ready ==
createdAt` — they never build.

The push to `main` created a deployment with `target: "production"`, which
**confirms `main` is the production branch**.

---

## 1. Mechanism call: merge to `main`, not repoint

`main` was already the production branch — the `checkthenfix-git-main-*` domain
exists and is aliased, and pushing to it produced a `target: "production"`
deployment.

**Rejected: repointing production at `design-pass`.** That would make the branch
receiving daily design work the branch serving the public — one bad push goes
live with no gate. **Rejected: `release`** — it is empty by design ("deliberately
empty until launch") and would serve nothing.

The merge was a clean **fast-forward**: 41 commits ahead, **0 commits on `main`
that `design-pass` lacked**. No merge commit, no conflict resolution, nothing
reconciled by hand.

### The superseded dates, verified before pushing

Not asserted from the guard alone. I built production from `main` and served it:

| Check | Result |
|---|---|
| `/` superseded pairing | **0 occurrences** |
| `/deadline` superseded pairing | **0 occurrences** |
| Corrected dates present | `April 26, 2027`, `April 26, 2028` |
| `/dev/states` in production | **404** |

The only remaining occurrence of the old pairing anywhere in the repo is the
planted mutation string inside the dated-claims guard's own test, where it
belongs.

---

## 2. Node override warning — diagnosed, not guessed

The warning was **the repo being loose, not the project being wrong**.

| | Before | After |
|---|---|---|
| Vercel project `nodeVersion` | `22.x` | `22.x` (unchanged) |
| `package.json` `engines.node` | `>=22.13.0` — **permits 24.x** | **`22.x`** |
| `.nvmrc` | absent | **`22`** |

Vercel was reporting that the project setting overrode what the repo declared it
accepts. The repo now states one version, and it is the one the project runs.
Local Node is v22.23.2 and satisfies it.

---

## 3. The verification triple

Run now, against what is reachable while paused:

```
PASS  engine health          HTTP 200 veraPDF pinned 1.30 actual 1.30.2
PASS  auth fails closed      unauthenticated /check -> HTTP 404
PASS  canonical 42           May agenda scored 42 (expected 42)
FAIL  deployed /             HTTP 302 -> Vercel wall (project paused)
FAIL  deployed /deadline     HTTP 302 -> Vercel wall (project paused)
FAIL  deployed /faq          HTTP 302 -> Vercel wall (project paused)
```

**The three that do not depend on Vercel are green.** The engine is healthy on
the pinned validator, refuses unauthenticated calls with a 404 (an unauthorised
caller learns nothing), and the canonical May agenda still scores exactly 42.

The three deployed-page checks cannot pass until Resume. They are not skipped —
they fail honestly and say why.

### The script was lying, and that is the more useful finding

`verify-deployment.ts` followed redirects. A paused project answers **302** to
Vercel's own wall, `fetch` followed it, and the line came back as:

> `HTTP 200, superseded dates: false`

Which is true **of the wall** and says nothing about our page. A verification
that reports PASS-shaped detail for a page it never reached is worse than one
that fails. It now uses `redirect: "manual"` and names the wall.

`scripts/verify-deployment.ts` is committed and reproducible. It accepts
`VERCEL_SHARE_TOKEN` for protected deployments.

---

## 4. Band label three

`The tool.` → **`The Answer.`** Title Case, body unchanged, live.

The ruling wrote it bracketed as pending confirmation, so it ships and is listed
for sign-off — the precedent accepted for the hero paragraph in round 6.4.

**Flagged rather than silently corrected:** `The rule` carries no trailing stop
while `The problem.` and `The Answer.` do. Those three labels are approved
verbatim, so the inconsistency is the owner's to resolve.

---

## 5. Step icon (mid-round instruction)

Moved to the card's **lower-right corner**. The card was already
`position: relative`, so it is a corner pin; the body copy gets 3rem of right
padding, without which a third line runs under the icon.

Verified at 1280 and 390 — text clears the icon on all three cards, so **it stays
rather than being removed**.

It is also last in the card now: decorative and `aria-hidden`, so a screen reader
reaches the step's words before it, and reaches nothing at all.

---

## What happens after Resume

Run:

```
doppler run -c stg -- tsx scripts/verify-deployment.ts <production-url>
```

Expected: all six lines PASS. If the deployed lines still 302, the project is
protected rather than paused and needs `VERCEL_SHARE_TOKEN`.

**Preview links in future reports will only resolve once the project is live** —
they cannot resolve today, and reports have been quietly assuming otherwise since
round 5b.

---

## Owner actions

1. **Click Resume Project.** Everything else is done and pushed.
2. Confirm **"The Answer."**, and rule on the missing stop in "The rule".
3. Carried: label colour and weight, the IFR permalink, the mobile CTA,
   `/terms` and `/privacy`, the marketing-campaign gate.
