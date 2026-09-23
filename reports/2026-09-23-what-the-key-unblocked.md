# What the key unblocked — and what it uncovered
## 2026-09-23 · Builder session

The key works. Everything it was blocking now runs, both halves of the guard are
proven in one session, and today's cost numbers are in.

The first run of the measuring scripts produced a table that was **entirely
wrong and reported no error**. Finding that is the most useful thing in this
report, so it is not buried at the bottom.

## 1. The key — gate passed

Both `dev` and `stg` hold a real `service_role` token issued by the provider,
and both name the **practice** project (`f7fa5a34a17d`), not the live one
(`0b91db6bd30a`). The placeholders are gone.

Read a row with it:

```
GET documents?limit=1  ->  HTTP 200
body: []
```

Empty because the table is empty, which is the right answer for a freshly loaded
practice database. The gate passed, so the session continued.

## 2. The guard — both halves, same session, same code

### The deletion half

Seeded five anonymous documents in the practice project: three past their
retention window, one still inside it, one with no window set.

```
retention window: 72h
anonymous documents past their window: 3
deleted: 3 rows, 0 stored objects
```

Survivors afterwards:

| token | window state |
|---|---|
| t-future | still in window |
| t-nullexp | no window |

**It deleted exactly the three expired rows and nothing else.** "0 stored
objects" is correct: the seeded rows name storage paths that were never written,
and removing a path that does not exist is not an error.

### The refusal half

Same code, same session, pointed at the live project:

```
Refusing to run the retention purge (scripts/purge-expired.ts).

This script deletes data, and it will only ever do that to the practice
copy of the database, never the real one. Right now it is pointed at a
database at "<redacted>", which is not the one set aside for practice.

Nothing has been touched and nothing has been deleted.
```

**The live project was never connected to.** The refusal happens before any
database module is loaded, which is what makes proving it safe.

Afterwards the practice project was returned to **0 rows and 0 benchmark
objects**.

## 3. The measuring scripts — and the bug they exposed

### What the first run said

```
2015-06-04-board-agenda.pdf   before  38   after  38
2026-APRIL-7-AGENDA.pdf       before  38   after  38
2026-MAY-5-AGENDA.pdf         before  38   after  38
...all seven identical...
```

Every document, before and after, scored 38. **The canonical document is 42 and
100, and that was proven two days ago.** No error was reported. The script wrote
its results and exited cleanly.

### What was actually wrong

Not the engine. Every measuring script uploaded each document to **one fixed
storage path** with "replace if present" set, then asked the engine to score that
path.

Uploading a second file to that path **returns no error and does not replace the
object.** Downloading it straight back returns the first file's bytes:

```
2015-06-04-board-agenda.pdf   localSha=04adb0c23087  storedSha=04adb0c23087  match=true
2026-MAY-5-AGENDA.pdf         localSha=cd742a552cdd  storedSha=04adb0c23087  match=false
```

So every measurement after the first was **the first document, scored again**.
The engine was reading the object faithfully; the object never changed.

The broken run had already overwritten the stored results. They were restored
from version control, and the fix is a path nothing has used for every upload,
cleaned up afterwards.

### After the fix

```
2015-06-04-board-agenda.pdf   before  38   after   95
2026-APRIL-7-AGENDA.pdf       before  42   after  100
2026-MAY-5-AGENDA.pdf         before  42   after  100
Agenda Package ... Dec01_2025 before  42   after   85
Agenda Package ... Mar04_2024 before  47   after   90
Agenda Package ... May11_2026 before  42   after   85
Agenda Package ... Oct13_2022 before  47   after   90
7 measured pairs
```

**Identical to the numbers that were already stored.** The engine had not
regressed at any point; the instrument was lying.

### measure-page-cost

Ran, produced 21 measurements — and the shrink guard **refused to write**,
because the file held 25. That refusal was correct and the smaller set was also
correct: a source document left the corpus in August, so the script has three
sources where it had four. Re-run with an explicit `--allow-shrink`, which is
now something a person types rather than something the guard is weakened for.

## 4. verify-deployment — every line

```
PASS  engine health          HTTP 200 veraPDF pinned 1.30 actual 1.30.2
PASS  auth fails closed      unauthenticated /check -> HTTP 404
PASS  canonical 42           May agenda scored 42 (expected 42)
FAIL  deployed /             HTTP 404
FAIL  deployed /deadline     HTTP 404
FAIL  deployed /faq          HTTP 404

NOT GREEN — see FAIL lines above
```

**The canonical-42 check now runs and passes.** The three remaining failures are
section 6.

## 5. Today's cost numbers

### Fidelity — measured today, 7 documents

| before | after | gain |
|---|---|---|
| 38 | 95 | +57 |
| 42 | 100 | +58 |
| 42 | 100 | +58 |
| 42 | 85 | +43 |
| 42 | 85 | +43 |
| 47 | 90 | +43 |
| 47 | 90 | +43 |

**Median 42 → 90, median gain +43. Two of seven reached 100.**

**Nothing moved.** These are the same numbers the previous report carried. The
difference is that the previous report was quoting an older run and said so;
these were measured today.

### Cost per page — measured today, 21 controlled points

| | |
|---|---|
| Median | **51.4 ms/page** |
| Range | 22.5 – 403.0 ms/page |
| Smallest step (≤10pp), median wall | **2.73s** |
| Largest measured (499pp), wall | **11.22s** |

By source, median wall time:

| source | small (≤25pp) | large (≥150pp) |
|---|---|---|
| mixed | 2.85s | 6.43s |
| image-heavy | 3.34s | 7.88s |
| long-budget | 4.21s | 8.77s |

### Did anything move?

Comparing the same source and page count, previous run against today:

| | |
|---|---|
| Points compared | 20 |
| Within 15% | **13** |
| Moved more than 15% | 7 |
| Median change | **+3%** |
| Median ms/page, previous → today | 55.8 → **51.4** |

**No, nothing meaningfully moved.** The scatter runs both ways (−29% to +31%),
which is what a shared container produces — the script itself takes the minimum
of repeated runs for exactly that reason. The one pattern worth watching is the
long-budget source at 200 pages and above, which trended up 28–31%; with two runs
per point that is suggestive, not a finding.

### With seal unwrapping

Re-measured today on the canonical seal, three runs: 5.24s, 5.21s, 5.22s.

| | |
|---|---|
| Added per document carrying a seal | **5.22s** (25 recogniser calls) |
| Documents carrying a seal | 13 of 30 |
| One-page document, no seal | **2.73s** |
| One-page document **with** a seal | **7.95s — 2.9×** |

No model or API cost is added; this is local processor time. **The money per
document is unchanged; the seconds are not, and the cheapest documents are still
hit hardest in relative terms.**

## 6. The three "not found" pages — it is the address

**The address is wrong. The pages are fine.**

The value staging uses as the site address fingerprints to **`f7fa5a34a17d`**,
and its shape is a **database URL**, not a website address. That fingerprint is
the practice project. So `verify-deployment` has been fetching `/`, `/faq` and
`/deadline` from a database API host, which returns 404 for all of them —
including `/robots.txt`, which no website would miss.

The same three paths on the real published address return **200, 200, 200**.

So: when the practice project's URL was distributed into the settings, it landed
in the site-address slot as well as the database slot. Nothing is missing from
the site and nothing is broken in the deployment.

**Not fixed, as instructed.** The fix is one value: set staging's site address
back to a website address. Worth noting the check is honest — it correctly
reported a failure; it was pointed at the wrong thing.

## Checks

Lint clean, types clean, **510 tests pass** (502 before, 8 added). The new tests
assert that no measuring script uploads to a hardcoded shared path, and that the
before/after script takes a fresh path *inside* its per-file loop — computing it
once per run is the same bug wearing a different hat. Putting the shared path
back fails 2 of them.

## One thing to carry forward

The shared-path bug produced a complete, plausible, internally consistent table
of wrong numbers, with no error anywhere — and it was only caught because one
value in it contradicted something proven two days earlier. A benchmark that
reuses one storage path has no way to notice when the reuse stops working, and
"it ran and wrote results" is not evidence that it measured anything.

## Not in this report

No credentials, hostnames, connection strings, entity names or document
contents. Databases and addresses appear as fingerprints only.
