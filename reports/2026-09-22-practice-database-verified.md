# The practice database, verified — and it was not what the brief described
## 2026-09-22 · Builder session

The practice project exists and the schema is loaded. **The Doppler
configuration did not match the brief, and one part of it was a hazard.** That is
the first finding and it changed what the rest of the session could do.

One credential is still missing, which blocks three of the seven steps. Said
plainly at the end, with exactly what unblocks them.

## 1. The fingerprints — read, not assumed

**Method:** `doppler secrets get NEXT_PUBLIC_SUPABASE_URL --plain`, with stdin
from `/dev/null` so the command cannot silently return an empty string. Each
result was then **positively identified** by matching it against the hash of a
project reference I could name — not merely checked against the markers that
have produced phantom findings before.

Both markers were computed first and neither appeared:

| | fingerprint |
|---|---|
| hash of an empty string | `e3b0c44298fc` |
| hash of this environment's redaction marker | `3930fb7a9a99` |

### What was actually there when the session started

| config | fingerprint | what it was |
|---|---|---|
| dev | `f7fa5a34a17d` | practice project |
| stg | `9ccc2e38c394` | **the old local throwaway — never changed** |
| prd | `f7fa5a34a17d` | **the practice project, not the live one** |

Three things were wrong:

1. **stg had not been changed at all.** It still pointed at the local database.
2. **prd pointed at the practice project** while holding the **live** project's
   keys. Confirmed independently: those keys are signed tokens that name their
   own project, and prd's named the live one. So production's settings named the
   practice database and carried live credentials.
3. **The practice keys were never added anywhere.** dev's URL had been updated
   but its keys were still the locally-minted ones from the old throwaway, which
   name no project at all. Reading the practice database with them returned
   **HTTP 401**.

No harm has occurred — production has no environment variables of its own, so
nothing reads prd's settings today. But a production configuration naming a
practice database is a trap, and it was worth finding before anything ran.

### What I corrected

- **prd's URL restored to the live project**, so it matches the live keys it
  holds and matches what the brief says prd is for.
- **dev and stg both pointed at the practice project**, with its real anon key.
- **Both given `SUPABASE_THROWAWAY_URL`** naming the practice project — see §4.
- **The service key in dev and stg set to a placeholder that says what to do.**
  Leaving a plausible-looking wrong key would fail confusingly; this fails
  loudly and names the fix.

| config | fingerprint now | |
|---|---|---|
| dev | **`f7fa5a34a17d`** | practice |
| stg | **`f7fa5a34a17d`** | practice |
| prd | **`0b91db6bd30a`** | live |

Dev and stg match each other and neither matches prd, which is what step 1 asked
for.

## 2. The schema

Applied through the management connection, because Doppler's credentials do not
authenticate. All six migrations, in order.

**7 tables, every one with 0 rows.** The live project has **7 tables** with the
same names. The counts match.

## 3. The local throwaway is retired

Stopped, and the two scripts that ran it are **deleted** — the local Postgres and
the shim that made it look like Supabase. There is now one answer to "where does
development point": the practice project, which has real file storage the local
one never did.

`supabase/tests/local_harness.sql` is kept, with its refusal intact, and now
carries a line saying what it is for: checking that a migration or a policy test
behaves on a throwaway Postgres, offline, before it touches a real project. Not
development.

## 4. The guard

The old guard asked "is this database on the machine running the script", which
was a complete answer while the throwaway was local. A hosted practice project
looks exactly like the live one from here, so something has to say which is safe
to delete from — **and it cannot be a flag, because an optional flag is what
failed in the first place.**

So it is **two settings that must agree**: one says where a script is pointed,
the other names which database may be deleted from, and a destructive script runs
only when they match on the host. Production's config defines only the first, so
it is refused — with no production hostname anywhere in the repository. And
repointing development at the live database does not quietly become allowed,
because then the two would disagree.

### Pointed at the practice project

```
purge: could not count expired documents: Invalid API key
```

**The guard allowed it.** The script got past the refusal and reached the
database, where it failed on the placeholder service key. The guard's decision is
proven; the deletion is not, because of the missing credential.

### Pointed at the live project

```
Refusing to run the retention purge (scripts/purge-expired.ts).

This script deletes data, and it will only ever do that to the practice
copy of the database, never the real one. Right now it is pointed at a
database at "<redacted>", which is not the one set aside for practice.

Nothing has been touched and nothing has been deleted.
```

Refused before opening any connection.

### And with no designation at all

Pointed at the **practice** project but with the designation removed — which is
what production's configuration looks like — it is **still refused**. Deny by
default survived the change.

### Mutations

| Mutation | Tests failed |
|---|---|
| guard always allows | 4 |
| designation matched by substring rather than equality | 1 |
| designation merely has to be set, not to agree | 2 |
| purge loads the database layer at the top level again | 1 |

## 5. The four scripts

| Script | Result |
|---|---|
| `measure-before-after.ts` | **blocked** — `upload failed: Invalid Compact JWS` ×8 |
| `measure-page-cost.ts` | **blocked** — same |
| `verify-deployment.ts` | **ran**, 2 pass / 4 fail |
| `city-survey.ts` | **ran** — it does not need file storage |

`verify-deployment` is worth reading on its own:

```
PASS  engine health       HTTP 200 veraPDF pinned 1.30 actual 1.30.2
PASS  auth fails closed   unauthenticated /check -> HTTP 404
FAIL  canonical 42        Invalid Compact JWS
FAIL  deployed /          HTTP 404
FAIL  deployed /deadline  HTTP 404
FAIL  deployed /faq       HTTP 404
```

The engine is healthy and its validator is the pinned version; unauthenticated
access fails closed. The rest is the missing key, and three deployed pages
returning 404 from the address staging is configured with.

The two blocked scripts **did not damage anything**: the guard added on
2026-09-20 refused to write their empty results over the real measurements, and
said so.

## A mistake of mine, and the gap it exposed

I ran `city-survey` with a three-city limit. It succeeded, and **overwrote a
fifty-entry mapping with three entries.** That file is deliberately not in
version control. It was recoverable only because a copy existed in the private
corpus, and I restored it — verified identical by checksum, 50 entries.

The guard written on 2026-09-20 refuses to write an **empty** result over real
data. **A partial run is not an empty run**, and the empty check never saw it.
The brief asked whether the mapping was safe from a failed run; it was safe from
a failed run and not from a *small* one, and I found that out by causing it.

It now refuses to **shrink** a results file unless the caller explicitly says the
smaller set is intended. Proven by re-running the same three-city command:

```
ShrinkingResultRefusal: path: 'corpus/city-survey/mapping.json', had: 50, now: 3
mapping after: 14735 bytes — UNCHANGED, the refusal held
entries: 50
```

Mutation-tested: never firing fails 2 tests, comparing the wrong way round fails 3.

## 6. The cost numbers

**These are measured, but not measured today** — the fresh run is blocked. They
come from the last successful measurement, plus the seal-unwrapping time measured
yesterday.

### Fidelity, 7 documents

| before | after | gain |
|---|---|---|
| 38 | 95 | +57 |
| 42 | 100 | +58 |
| 42 | 100 | +58 |
| 42 | 85 | +43 |
| 42 | 85 | +43 |
| 47 | 90 | +43 |
| 47 | 90 | +43 |

**Median 42 → 90, median gain +43. Two of seven reached 100.** Every run
completed cleanly; none declined.

### Time, 32 documents measured

| | |
|---|---|
| Median ms per page | **909** |
| Range | 3.7 – 3,229 ms |
| Median wall time, all documents | **2.80s** |
| Median wall time, one-page documents | **2.60s** |

### With seal unwrapping added (measured 2026-09-21)

| | |
|---|---|
| Added per document carrying a seal | **+4.9s** (25 recogniser calls) |
| Documents carrying a seal | **13 of 30 (43%)** |
| One-page document, no seal | **2.60s** |
| One-page document **with** a seal | **7.50s — 2.9×** |
| Averaged across the corpus | **+2.1s per document** |

No model or API cost is added; unwrapping is local processor time. The money per
document is unchanged. The **seconds** are not, and the cheapest documents are
hit hardest in relative terms — which are exactly the single-page agendas the
pricing was built around.

## 7. CLAUDE.md

Updated. It now names the hosted practice project instead of a local database,
records the two-settings rule and why it is not a flag, and lists both
fingerprints that mean "you did not read a value", because each has already
produced a phantom finding once.

## What could not be done, and what unblocks it

**The practice project's `service_role` key is not in Doppler and cannot be
obtained from here.** The management connection deliberately exposes only
publishable keys; the project's signing secret is not readable from the database;
and the dashboard needs a password, which the Builder does not enter.

That one credential blocks:

- the **deletion half** of the guard proof (the refusal half is proven);
- **`measure-before-after`** and **`measure-page-cost`**, and so a *fresh*
  fidelity and cost-per-page measurement;
- **`verify-deployment`'s canonical-42 check.**

**One step unblocks all of it:** copy the practice project's `service_role` key
from its API settings into Doppler `dev` and `stg` as
`SUPABASE_SERVICE_ROLE_KEY`. The placeholder currently in both says the same
thing when a script hits it.

Separately, three deployed pages return 404 from the address staging points at —
worth a look, and not something this session changed.

## Not in this report

No credentials, connection strings, hostnames, entity names or document
contents. Databases appear as fingerprints only.
