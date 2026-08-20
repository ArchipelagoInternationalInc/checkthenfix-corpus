# Overnight campaign gate — DOI armed, carried items, the 50-city artifact, remediation seam

**Date:** 2026-08-20 · `main` and `design-pass` identical at `c0fd555` · 343 tests, lint and typecheck clean.

---

## DEPLOYMENT STATE — read first, it is still red

> **`checkthenfix.vercel.app` is publicly serving a pre-5b build carrying the
> superseded April 2026 / April 2027 deadline pairing. Verified again at the end
> of this session: HTTP 200, one occurrence, no auth wall.**

**The exposure could not be closed, and no move remained that I could execute.**

| Attempted | Result |
|---|---|
| Rebuild production from current `main` | Deployment **BLOCKED** — project `live: false` |
| Enable SSO protection on production | **428** — not available on this plan |
| Enable password protection | **428** — Advanced Deployment Protection not on team |
| Repoint the production branch | **No tool** — MCP has no project-settings write |
| Delete the stale deployment | **No tool** — MCP has no deployment delete |

There is no Vercel token in any Doppler config (dev, stg, prd checked) and the
CLI is logged out, so the REST API is unreachable. A CLI device-login was
triggered accidentally during a commit and killed — I did not complete it.

**Protection was not retried after the ruling.** It was attempted exactly twice,
both before the ruling arrived, both refused. Nothing was purchased; the
effective protection config is byte-identical to before.

### The wedge

Polled across ~25 minutes: `live: false` throughout, including after a fresh
push to the production branch. The telling detail — **`updatedAt` advances only
when I touch the project**, and did not move when Resume was clicked, which
suggests the action never registered. Blocked deployments show
`buildingAt == ready == createdAt` and an `errorLink` pointing at *team*
configuration.

Evidence pack for the ticket: **`VERCEL_SUPPORT_EVIDENCE.md`**.

### Two owner clicks that end it in 30 seconds

- **Settings → Git → Production Branch** → a branch name that does not exist.
- **Deployments → current production deployment → Delete.**

### Exposure window

Public serving of superseded dates began when the owner resumed (the alias had
been behind the pause before that) and **is still open**. I cannot state a close
time because it has not closed.

---

## Slice A — newsletter armed

`BREVO_LIST_ID=19`, `BREVO_DOI_TEMPLATE_ID=1` set in dev and stg Doppler.
`newsletterConfigured()` returns **true**. Values went Doppler-side only.

The config test now asserts the transition **in both directions**: all three
values arm it, and each removed in turn must shut it. Asserting only the armed
state would pass trivially once the env happens to be set; asserting only the
closed state would have stopped being true tonight. Non-numeric ids are refused
rather than posted.

`scripts/doi-test-send.ts` is staged and **refuses a bare invocation** — the
layer is armed, so it really would mail a real inbox. Needs `--yes`, masks the
address on output. **No address was sent to tonight.**

---

## Slice B — carried items

- **Mobile CTA**: fixed at the ruled 390px — two lines at 20px, still larger than
  the 18px it replaced. Below ~380px it remains three visual lines; fixing that
  means returning to roughly the pre-6.4 size, so it is reported, not done.
- **`/dev/gate-review`** ships: the whole staged Slice-A set as real pages, plus
  by-eye side-by-sides for both blue candidates and both Oswald weights.
- **State pages parked** as **D-018**, reasoning kept.
- **IFR permalink**: the *spec* is written beside the placeholder so whoever
  supplies it knows the shape. A guessed document number would not be honest.

### How gate-review and the quarantine guard are both true

The guard's rule is that no **user-facing surface** renders staged copy ungated.
`/dev/gate-review` is not one: robots disallows `/dev`, the page sends noindex,
nothing links it, and it labels every draft as a draft. The test now enumerates
exemptions **individually**, so a third is a deliberate edit — writing it
surfaced a second existing importer I had forgotten (the sample-lane decline in
`/api/check`, behind a flag that is false).

---

## Slice C — the 50-city artifact

| | |
|---|---|
| Municipalities attempted | 50 |
| Yielding a checkable document | **17** |
| Documents checked | **27** |
| Scores | min **14** · median **38** · mean **42.7** · max **80** |
| Under 50 | **19** (70.4%) |
| Perfect 100 | **0** |

> **The denominator is 27 documents from 17 cities, not 50** — and the draft
> headline says so. A "50 cities" headline would be false, and it is exactly the
> number this artifact is tempted toward.

33 of 50 produced nothing: 22 no findable document, 8 no readable robots.txt,
2 unreachable, plus 5 responses that were not PDFs.

**The rules, implemented not promised:** robots.txt honoured with *unreadable
means do not fetch* (8 cities skipped on that rule alone); 2s throttle floor,
one host at a time, Crawl-delay respected when longer; **no municipality named**
in anything shippable — asserted by grepping all 50 seeded names against the
aggregate — with the mapping in gitignored `corpus/city-survey/`.

**Two things the first version got wrong, both caught by running it:** discovery
required a `.pdf` extension and found nothing on five of six trial cities, because
municipal CMSes serve agendas from extensionless handlers; and once the filter
broadened, downloads had to be verified by **magic number**, which rejected 5
non-PDFs that would otherwise have been scored into the artifact.

The copy carries its own sampling caveat: not a random sample, cannot support a
population estimate.

---

## Slice D — the seam

**Cut declared in `REMEDIATION_SEAM.md`.** Verified below the line; nothing above
it started.

Engine A **runs locally** — 122 marked-content items, language and title set, on
a corpus document. The validator does **not**: no Java here, so every re-run is
container-bound, which the pipeline design has to start from.

The before/after loop is already proven (38→95, 42→100, 42→85, 47→90). What does
not exist is the product surface around it.

**Why the cut is here:** everything above the seam is user-facing or
money-adjacent — a results page needs strings, a Remediation Report PDF is a
record a customer keeps, and "uncharged by construction" is a billing claim.
Half-built versions of those look finished, which is worse than absent.

---

## Delegation log

| # | Call |
|---|---|
| 1 | Empty commit as the deploy trigger — no code change was needed, and hiding a trigger inside unrelated work would obscure it. |
| 2 | Branch reconciliation: merged `main` into `design-pass` so `main` stays linear and parity is checkable. |
| 3 | Gate-review exemption enumerated individually rather than by prefix. |
| 4 | DOI test script refuses bare invocation and masks addresses. |
| 5 | Survey discovery broadened from `.pdf` to path shape; downloads verified by magic number. |
| 6 | Unreadable robots.txt treated as refusal, not permission. |
| 7 | Artifact headline uses 27/17, not 50. |
| 8 | Slice D cut at the surface boundary. |
| 9 | Did not complete the Vercel CLI device-login that a plugin hook triggered. |

---

## Owner sitting

1. **`/dev/gate-review`** — the big read (localhost; production is wedged).
2. **By-eye picks**: blue `#0E7490` vs `#1C6E9C`; Oswald Regular vs Light.
3. **DOI test send** — `doppler run -c stg -- tsx scripts/doi-test-send.ts <you> --yes`.
4. **The 50-city numbers** and artifact draft.
5. **The exposure** — two clicks above, and the support ticket.
6. D-017 riders (30-day paid-guest retention, claim-into-account) **not written**
   — Slice D stopped at the seam before them.
