# One value, and the thread closes
## 2026-09-23 · Builder session · second entry today

`verify-deployment.ts` is **TRIPLE GREEN**. The database and measurement work is
complete. Seven things remain before launch and they are listed at the end.

## 1. The value

Staging's site address held the practice **database's** URL. It now holds the
site's published address.

| | fingerprint |
|---|---|
| before | **`f7fa5a34a17d`** — the practice database |
| after | **`bddbd3875ade`** — the published site |

The same four paths that reported "not found" immediately afterwards:

```
/            HTTP 200
/faq         HTTP 200
/deadline    HTTP 200
/robots.txt  HTTP 200
```

Nothing was ever wrong with the pages.

## 2. The check that stops it recurring

The rule is a **relationship, not a list of vendors**: *the site's address must
not be the database's address.* That holds whoever hosts either one, and it is
precisely the mistake that was made. A second, weaker net catches a
database-shaped host when there is no database address configured to compare
against.

It is applied in two places:

- **`siteUrl()` ignores a bad value and falls back**, rather than publishing it.
  A wrong address does not just break a check — it goes into the sitemap and into
  emailed report links. It deliberately does **not** throw: this runs while a
  page is rendering, and a misconfigured setting should not take the site down.
- **`verify-deployment` refuses before fetching anything.** That is where the
  cost was: three pages reported broken, all of them fine.

### Proved by putting the wrong value back

```
PASS  engine health          HTTP 200 veraPDF pinned 1.30 actual 1.30.2
PASS  auth fails closed      unauthenticated /check -> HTTP 404
PASS  canonical 42           May agenda scored 42 (expected 42)
FAIL  site address           it is the database's address, not the site's —
                             refusing to test pages against it

NOT GREEN — see FAIL lines above
```

One line naming the actual problem, instead of three symptoms — and it never
fetched from the wrong host.

### Mutations

| Mutation | Tests failed |
|---|---|
| the check never refuses anything | 5 |
| drop the database-address comparison | 3 |
| `siteUrl` publishes the bad value anyway | 1 |
| `verify-deployment` fetches before checking | **0 → 1** |

**The fourth initially passed, and that is worth recording.** The structural test
asserted that a variable named `addressProblem` appeared before the fetch. The
mutation kept the name and replaced its value with a literal `null` — so the
assertion held while the guard did nothing. A test that checks a variable name
is protecting a variable name. It now asserts the **call** and the **branch**,
and fails as it should.

## 3. Every line green

```
PASS  engine health          HTTP 200 veraPDF pinned 1.30 actual 1.30.2
PASS  auth fails closed      unauthenticated /check -> HTTP 404
PASS  canonical 42           May agenda scored 42 (expected 42)
PASS  deployed /             HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /deadline     HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /faq          HTTP 200, superseded dates: false, corrected dates present: true

TRIPLE GREEN
```

Lint clean, types clean, **518 tests pass**.

## What is finished

- The practice project is the throwaway: hosted, real file storage, schema
  loaded, 7 tables matching live. Development and staging point at it; production
  points at the live project.
- The destructive guard is proven **both ways in one session** — deleting exactly
  the expired rows in the practice project, and refusing the live project before
  opening a connection.
- All four measuring scripts run, with current numbers.
- The local throwaway is gone; there is one answer to where development points.

**Three classes of silent lie are now guarded**, each with tests that fail when
the guard is removed: a results file refuses to be emptied *or shrunk*; every
benchmark upload gets a path nothing has used; and a site address that is really
a database address is refused before anything is fetched from it. All three were
found the same way — a result that looked complete and was wrong.

## What remains before launch

1. **Production holds 3 of the settings it needs.** Absent: the service key, the
   scheduled-task secret, the engine address and secret, the email keys, the
   judgment keys. Observed today: the scheduled purge endpoint returns **503**
   because its secret is unset, which is it failing closed and correct. **The
   customer-record path is not proven working and cannot be until the service key
   is there.** Launch blocker.
2. **The real domain is not attached.**
3. **Owner copy approval** outstanding.
4. **Reverse the crawler block** — only when 2 and 3 are both true. The report
   path stays disallowed permanently afterwards; that is privacy, not SEO.
5. **The public record repository's old objects are still served.** Checked
   today: it reports 173 KB, and the canonical document still fetches from a
   pre-rewrite commit at its full size. **Garbage collection has not run.** The
   request wording is in the 20 September report.
6. **Payments are in test mode.** Going live is a deliberate decision.
7. **A pricing decision on seal unwrapping**: +5.22s on any document carrying a
   round seal, 13 of 30 in the corpus. A one-page document goes 2.73s → 7.95s.
   The money is unchanged; the seconds are not, and the cheapest documents move
   most in relative terms.

## Not in this report

No credentials, hostnames, connection strings, entity names or document
contents. Addresses and databases appear as fingerprints only.
