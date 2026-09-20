# Closing the database safety gap
## 2026-09-20 · Builder session

The 17 September report found one database, two settings files pointing at it,
and a delete script guarded by an optional flag. The protection now exists. Two
things could not be done as specified and are set out at the end, along with one
mistake of mine that the owner should know about.

**Fingerprint convention, unchanged from 17 September:** the configured database
URL with any trailing newline stripped, SHA-256, first twelve characters. No
hostnames, connection strings, or credentials appear anywhere in this report. A
fingerprint of `e3b0c44298fc` would mean an empty read, not a value — it appears
nowhere below because every read was checked.

## Step 1 — Where the live site reads its database from

**None of the three settings files.** The deployment platform's production
environment has **zero** environment variables for this project. Not a missing
database entry: no variables at all.

Three independent confirmations:

| Check | Result |
|---|---|
| Production environment variables | **0** |
| Preview environment variables | 18 |
| Database URL compiled into the live page bundle | **none present** (8 script files fetched and searched) |
| A live page that needs the database | **HTTP 500** |

The third check matters because this framework compiles `NEXT_PUBLIC_` values
into the browser bundle at build time. Had production ever been built with a
database configured, the URL would be sitting in the served JavaScript. It is
not there, which agrees with the variable count rather than merely not
contradicting it.

So the live site has no database at all, and the pages that need one are
returning server errors to the public right now. **That is a launch blocker and
it is not the one this session was sent to fix.** It is unrelated to the safety
work; it was found while answering this question.

**One more thing, and it is an open question I could not close.** The preview
environment's database URL fingerprints to `3930fb7a9a99`, which matches **none**
of the four databases in this account, in any of four URL spellings. Preview
deployments are pointed at a database this account does not appear to own —
stale, deleted, or belonging elsewhere. I could not determine which from here.

## Steps 2 and 3 — The throwaway, and the fingerprints

A second hosted project was the obvious answer and it **costs $10/month**, which
the brief excluded. So the throwaway is local and free, built by
`scripts/throwaway-db.sh up`:

- real Postgres, database `ctf_throwaway`
- all six migrations applied, all seven tables present, **0 rows**
- PostgREST in front of it, because the application's client library talks to
  that rather than to Postgres directly
- a small shim mapping the client library's paths onto PostgREST

The repository already contained `supabase/tests/local_harness.sql`, written for
exactly this purpose, including the macOS start-up gotcha. This followed it
rather than inventing a second way.

### The fingerprints

| Setting | Fingerprint |
|---|---|
| development | **9ccc2e38c394** |
| staging | **9ccc2e38c394** |
| the throwaway | 9ccc2e38c394 |
| **the live database** | **0b91db6bd30a** |
| production (deployment platform) | *no database configured — see step 1* |

Development and the live database **do not match**, which is what step 3 asked
for. The comparison the brief specified — development against production — could
not be made in those terms, because production has no database setting to
compare against. The pair above is the meaningful one: development no longer
holds the live database's address or its key.

## Step 4 — The refusal

`lib/db/destructive-guard.ts`. Not a flag, and there is no way to switch it off.

It **denies by default**: it allows a database on the machine running the script
and refuses everything else. It does not look for production and block it. That
choice matters twice over — no production hostname has to be written into the
repository, and a database nobody has seen before (a new project, a colleague's
copy) is refused rather than waved through. Listing what is safe fails closed;
listing what is dangerous fails open.

The check reads one environment variable and compares strings. It opens no
connection, which is what makes proving it harmless.

### Proof run 1 — pointed at the throwaway, it deletes what it should

Seeded five anonymous documents: three past their retention window, one still
inside it, one with no window set.

```
retention window: 72h
anonymous documents past their window: 3
deleted: 3 rows, 6 stored objects
```

Two rows survived: the one still inside its window, and the one with no window.
It deleted the three it was supposed to delete and nothing else.

### Proof run 2 — pointed at the live database, it stops before anything

Run with the live address and, as a second independent safeguard, a deliberately
invalid key — so that even a broken guard could not have authenticated.

```
Refusing to run the retention purge (scripts/purge-expired.ts).

This script deletes data, and it will only ever do that to the throwaway
copy of the database on your own machine. Right now it is pointed at a
database at "<redacted for this report>", which is not the throwaway.

Nothing has been touched and nothing has been deleted.

What to do: start the local throwaway database, point your development
settings at it, and run this again. If you did mean to clear out the real,
live database, this script is not the way to do it — that job belongs to
the scheduled task that runs on the server.
```

`--dry-run` is refused too. A dry run still reads the live database, and the
instruction was "before any connection to any database".

**This session never connected to the live database.** The guard refused first,
both times.

### Two defects this uncovered

**The guard could not have worked as first written, and the reason is invisible.**
The database modules begin with `import "server-only"`, a package that throws on
purpose outside a server build. Top-level imports are hoisted, so that import ran
— and crashed — *before* the guard could fire. The database layer is now loaded
by `await import(...)` below the check. A test asserts that ordering against the
script's own source, because no unit test of the guard can see it.

**The delete script could never have run at all.** Its documented command needs
`--conditions react-server`, without which the same package aborts the process.
It has been documented without that flag since it was written. The most likely
reason nothing ever went wrong is that the script never started.

## Step 5 — The sweep

Everything in the repository that can delete rows, drop a table, or overwrite in
bulk. Harmless findings are listed too, so the sweep can be seen to be real.

### Guarded

| What | Why |
|---|---|
| `scripts/purge-expired.ts` | Deletes rows. The one we knew about. |
| `supabase/tests/local_harness.sql` | Creates schemas and would corrupt a real project. Its only protection was a comment saying not to. It now **raises** unless the database is named like a throwaway — and the hosted database is named `postgres`, so it can never match. Proven in both directions. |

### Assessed and deliberately not guarded

| What | Why not |
|---|---|
| `app/api/cron/purge/route.ts` | The **sanctioned** production path, on an hourly schedule. Guarding it would stop retention running in production and break the auto-delete promise made to every uploader. This is the job's proper home. |
| `lib/db/retention.ts`, `lib/db/documents.ts` | Library code the running product calls to do its ordinary work. Guarding them would guard the application against itself. |
| `supabase/tests/policies.test.sql` | Contains `delete from`, but the whole file is one transaction ending in `rollback` — verified, lines 20 and 332 — and is deliberately safe against a live project. Guarding it would break a tool that is already safe. |
| `supabase/migrations/0006_*.sql` | `drop constraint if exists`, which is what a migration is for. Applied deliberately, not by a script. |
| `scripts/verify-deployment.ts`, `measure-before-after.ts`, `city-survey.ts`, `measure-page-cost.ts` | Each uploads one scratch file and removes **its own** scratch file. No rows, no tables, no bulk overwrite, so none meets the brief's definition. Worth knowing: until today they were documented to run against staging, which pointed at the live project, so they were writing scratch objects into the live storage bucket. They now cannot — see the limits below. |

### Not databases at all

`scripts/lib/cdp.ts` (an in-memory map of pending browser messages),
`lib/rate-limit.ts` (an in-memory map of rate-limit buckets),
`scripts/verify-hero-white.ts` (removes an element from a web page),
`lib/payments/stripe.ts` and `scripts/stripe-walkthrough.ts` (payment-provider
API calls, already behind the separate refusal that rejects a live payment key).

## Step 6 — The rule

Written into `CLAUDE.md`: destructive scripts never run against the live
database; development and staging always point at the throwaway; any new script
that deletes or overwrites gets the refusal before it is committed. The section
also records the two traps above, because both are the kind that look like
working code.

## Checks

Lint clean, types clean, **448 tests pass**. The guard's own tests were
**mutation-tested**: making it always allow, switching exact host matching for
substring matching, and converting the dynamic imports back to top-level imports
each make the suite fail. The tests detect the failures they claim to.

## What could not be done, and one mistake

**A second hosted database.** $10/month, and the brief said it must cost nothing.
Local instead.

**The throwaway has no real file storage.** A local Postgres cannot serve it, and
the tool that would emulate it needs software this machine does not have. One
call is emulated — the single removal the retention sweep makes — because the
sweep refuses to delete rows unless storage removal reports success, so without
it the row half could not be exercised at all. Consequences, stated plainly:

- **The storage half of retention is not proven locally.** Proof run 1 proves the
  rows. The "6 stored objects" in its output is the emulation answering, not
  files being deleted.
- **The four measurement scripts above can no longer run**, because they need
  real storage and development now points at the throwaway. That is a real cost
  of this change, not an oversight. Restoring them means either a hosted
  throwaway at $10/month or installing container software on the build machine.

**My mistake, and it cost something.** Before overwriting the development and
staging settings I made what I believed were backup copies, using the settings
tool's own "clone" command. **A clone is not an independent copy — it tracks the
original.** A marker value written to development appeared immediately in the
"backup". So when I overwrote the settings, the live database's address and key
were overwritten with no copy behind them. I deleted the two misleading configs
rather than leave something named "backup" that is not one.

The address was recovered from the database provider's API and is confirmed
correct by fingerprint. **The live database's service key is no longer stored in
Doppler.** It is not lost — it can be read from that project's API settings page
in one click — but someone has to do that, and if production is ever wired up it
will be needed. I did not fetch it myself: pulling a live credential into a
session that had no use for it would have been the wrong trade.

## Also recorded this session

`D-020` in `DECISIONS.md`: the market-selection test is advisory, not a gate; the
project proceeds to launch and the test's findings are folded into the market
brief as revisions.

## The two things worth acting on next

1. **The live site's database pages are returning server errors to the public.**
   Production has no database configuration at all. This is a launch blocker.
2. **Preview deployments point at a database this account does not own.** Worth
   knowing what it is before launch.
