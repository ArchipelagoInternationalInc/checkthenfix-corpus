# Putting the live site together
## 2026-09-20 · Builder session · five jobs, three done, two blocked

Jobs 3 and 4 are done and proven against the live site. Job 5 is answered, and
the answer is that **my own finding last session was wrong** — that correction is
the most important thing in this report. Jobs 1 and 2 are blocked on one missing
credential, and the block is the same one in both.

## Job 5 first, because it is a correction

**There is no unknown database. I invented it, and I am sorry for the noise.**

The 20 September report said preview deployments point at a database fingerprinting
to `3930fb7a9a99` that matched nothing in this account. That value is:

```
sha256("[SENSITIVE]")[0:12] = 3930fb7a9a99
```

`[SENSITIVE]` is the redaction marker this environment writes in place of secret
values. Every one of the eighteen preview variables comes back as that same
eleven-character string. I fingerprinted the redaction, not the database, and
then reported that it matched no known project — which of course it did not.

It is the same failure mode as the empty-string hash I flagged last session, and
I flagged that one while walking straight into this one. The lesson I should have
taken is broader than "check for empty": **a fingerprint of an unread value is
not evidence of anything.** Both markers are now known: `e3b0c44298fc` for an
empty read, `3930fb7a9a99` for a redacted one. Treat either as a failed read.

**What preview actually points at: I could not establish it.** The value is
redacted in the pulled file, and the platform CLI has no command that prints a
single variable — only `add`, `rm`, `ls` (names only) and `pull` (redacted). The
deployment's own bundle would contain it, but preview sits behind an access wall.
Determining it needs the platform's web dashboard.

**I did not connect to it.** Nothing in this session opened a connection to any
database except the local throwaway.

## Job 1 — The practice database: BLOCKED

**I could not create the hosted project.** Three routes, all closed:

- The database provider's tool interface requires a cost-confirmation call whose
  amount must be a number. This environment sends every value as a string, so the
  call is rejected before it reaches the provider. Not a permission problem — a
  type mismatch I cannot work around from here.
- The provider's HTTP API needs a personal access token. There is none on this
  machine, none in any settings config for any project, and no provider CLI
  installed.
- The web dashboard needs a password, which I do not enter.

So there is no hosted practice database, and **development and staging still point
at the local throwaway** — fingerprint `9ccc2e38c394`, against the live database's
`0b91db6bd30a`. That is the safe state, unchanged, so nothing regressed. The local
one was not taken out of the way, because removing it would leave development
pointing at nothing while the thing meant to replace it does not exist.

**The owner unblocks this in one step:** create a provider access token and put it
in Doppler, or create the project in the dashboard. Either one, and the rest of
job 1 is mechanical.

### The four measuring scripts — what ran and what they returned

Three were run. Actual output:

```
──── scripts/measure-before-after.ts ────
  upload failed: throwaway has no Storage service; only retention's remove call is emulated
  upload failed: throwaway has no Storage service; only retention's remove call is emulated
  upload failed: throwaway has no Storage service; only retention's remove call is emulated
  0 measured pairs -> corpus/BEFORE_AFTER.json

──── scripts/measure-page-cost.ts ────
  upload failed: throwaway has no Storage service; only retention's remove call is emulated
  0 controlled measurements -> corpus/PAGE_COST_CONTROLLED.json

──── scripts/verify-deployment.ts ────
  PASS  engine health          HTTP 200 veraPDF pinned 1.30 actual 1.30.2
  PASS  auth fails closed      unauthenticated /check -> HTTP 404
  FAIL  canonical 42           throwaway has no Storage service
  FAIL  deployed /             HTTP 302 -> access wall
  FAIL  deployed /deadline     HTTP 302 -> access wall
  FAIL  deployed /faq          HTTP 302 -> access wall
  NOT GREEN — see FAIL lines above
```

Two genuine passes worth keeping: the **engine is healthy and its validator is
the pinned 1.30.2**, and **unauthenticated access to the check endpoint fails
closed**. Everything else needs file storage, which the local throwaway cannot
provide — exactly the limit recorded last session, now with the output to prove
it rather than a prediction.

`scripts/city-survey.ts` was **deliberately not run.** It rewrites the survey's
document-to-entity mapping, and that file is excluded from version control, so a
failed run would destroy data nothing could restore. It cannot succeed for the
same storage reason, so running it risked real loss for no information.

### A defect found by running them

**These scripts overwrite their results file even when they measured nothing.**
Both runs above wrote an empty array over real data: one file went from 1,160
bytes to 3, the other from 2,900 to 3. Both were tracked in version control and
were restored intact, so nothing was lost — but a failed run silently destroying
the previous measurements is a trap, and `city-survey.ts` writes one file that
version control would not have saved. Worth a guard: do not write a results file
when the result set is empty.

## Job 2 — Plugging the live site in: PARTIAL

**Set, from values I could legitimately obtain:**

| Variable | Source |
|---|---|
| `NEXT_PUBLIC_SITE_URL` | the site's current platform address |
| `NEXT_PUBLIC_SUPABASE_URL` | the provider's API — verified by fingerprint `0b91db6bd30a` |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | the provider's API; public by design, it ships in the browser bundle |

Production had zero variables and now has three.

**The service key could not be fetched, so Doppler is still incomplete.** The
brief said to take it from the project's settings page. That page needs a
password, which I do not enter. The provider's tool interface deliberately
exposes only publishable keys, not this one. The copy stored in the platform's
preview environment is unreadable for the same redaction reason as job 5.

Deployed anyway, because jobs 3 and 4 needed a live deployment to be proven.

### Pages tested, and what each returned

| Page | Result |
|---|---|
| `/` | **200** |
| `/faq` | **200** |
| `/deadline` | **200** |
| `/feedback` | **200** |
| `/r/<nonexistent token>` | **500** |
| `/api/check` (GET) | **405** — correct; it is a POST endpoint |
| `/api/cron/purge` (unauthenticated) | **503** |

The marketing site works. The parts needing customer records **do not**, and the
platform's own logs say exactly why:

```
GET /r/probe-does-not-exist-000 500
  Error: Supabase is not configured: NEXT_PUBLIC_SUPABASE_URL and
  SUPABASE_SERVICE_ROLE_KEY must be set (via Doppler).

GET /api/cron/purge 503
  cron/purge: CRON_SECRET is not set — refusing to run unprotected
```

So **two secrets stand between this and working**: the database service key and
the scheduled-task secret. The purge endpoint refusing to run unprotected is the
correct behaviour, not a fault — it fails closed.

Also still absent from production, and needed before the product functions
end to end: the engine address and secret, the email provider's key, and the
judgment model's key. None of those are values I hold.

## Job 3 — Search engines shut out: DONE

Both places, driven by one flag (`blockSearchEngines` in `lib/config/launch.ts`)
so they cannot drift apart. Fetched from the live site:

```
$ curl https://checkthenfix.vercel.app/robots.txt        [HTTP 200]
User-Agent: *
Disallow: /
```

No sitemap is advertised, deliberately: handing a crawler a list of pages it has
just been told not to fetch is a mixed message.

```
$ curl -I https://checkthenfix.vercel.app/
HTTP/2 200
x-robots-tag: noindex, nofollow

/faq        -> x-robots-tag: noindex, nofollow
/deadline   -> x-robots-tag: noindex, nofollow
/sitemap.xml -> x-robots-tag: noindex, nofollow
```

A third layer turned out to be present already: the pages' own metadata carries
`<meta name="robots" content="noindex, nofollow">`. So the site now refuses
crawlers in the file, in the header, and in the page.

`CLAUDE.md` carries a section headed **REVERSE ON LAUNCH DAY**, stating that the
flag flips only when the real domain is attached **and** the owner has approved
the copy — both, not either — and that `/r/` stays disallowed permanently
afterwards, because that one is a privacy rule and not an SEO one.

## Job 4 — The sitemap address: DONE

It published `http://localhost:3000` because production had no settings and the
address function fell through to its development default. It now derives the
address from the platform's own variables, which are present without
configuration — so a deployment given no settings still knows where it lives.

Live output:

```
$ curl https://checkthenfix.vercel.app/sitemap.xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
<url><loc>https://checkthenfix.vercel.app</loc><changefreq>weekly</changefreq><priority>1</priority></url>
<url><loc>https://checkthenfix.vercel.app/faq</loc>...</url>
<url><loc>https://checkthenfix.vercel.app/deadline</loc>...</url>
<url><loc>https://checkthenfix.vercel.app/feedback</loc>...</url>
```

4 URLs, **0 occurrences of "localhost"**, all four on the live host.

The test the brief asked for is `lib/__tests__/site-url.test.ts`. It walks every
combination a deployment can present and requires that none yields `localhost`,
checks the sitemap's own entries, and asserts the two crawler blocks read the
same flag. **Mutation-tested:** restoring the old fallback fails five of its
tests; switching off one crawler block but not the other fails one.

One related fix: the function reporting whether the address is usable now asks
about the resulting address rather than about one variable, so the queue stops
warning that email links "will point at the development host" when they will not.

## Checks

Lint clean, types clean, **457 tests pass** (448 before, nine added).

## What is blocked, in one place

1. **A provider access token, or the project created in the dashboard** — unblocks
   all of job 1.
2. **The database service key, from the project's settings page** — unblocks the
   customer-record half of job 2. Add `CRON_SECRET` at the same time.

Both need someone who can sign in. Neither is work I can finish from here, and
saying so is more useful than a report claiming five of five.

## On the constraints

No credentials, connection strings, or document contents appear above. Database
and engine addresses are given only as fingerprints. The site's **own public
address** does appear, in the fetched outputs — it is the product's public
marketing URL, already published in the site's own metadata, and the proofs for
jobs 3 and 4 are worthless without it. Flagging the choice rather than making it
quietly.
