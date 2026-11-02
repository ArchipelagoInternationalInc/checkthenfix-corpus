# The legal posture wording is live for the owner's read. The delete guard had a hole, now closed.
## 2026-11-01 · Builder session

Every placement the brief asked for that the product can carry is on
production, and the site is still closed to search engines. Section 8 lists the
placements the product cannot carry yet, because the pages they name do not
exist.

Section 1 matters most. Before deploying, the Builder ran the Definition of
Done checks by hand and found that **the guard keeping destructive scripts off
the live database did not work in production**. Nothing was deleted. The guard
is fixed, a test proves the fix, and production was deployed with the fix in it.

**Still open at filing time:** check 13 (the one-row public path) has not
completed, for a reason on this machine (section 6). This report is updated
when it runs.

---

## 1. The delete guard allowed the live database

**What was supposed to happen.** Destructive scripts call a guard first. It
allows a database on this machine, or a hosted database that a second setting
names as the practice copy. Production deliberately does not define that second
setting, so production is refused.

**What happened.** DoD check 7 runs the retention purge under the production
settings and expects a refusal. It got none. The script exited 0, opened a
read connection, counted **0** expired rows, and stopped. It deleted nothing
only because there was nothing to delete.

**Why.** The guard read the second setting and passed it to a helper whose
default argument is the first setting. When the second setting is *absent*,
not empty, the default fills in. So the helper returned the target address, the
guard compared the target with itself, and the two matched. The guard allowed
every database whenever the designation was missing, and production's settings
have exactly that shape.

**Why the tests passed.** The unit tests check the same case, but the test
process has no target address set either, so the fallthrough resolved to
nothing and refused. The tests were green by accident.

| | |
|---|---|
| rows deleted | **0** |
| live connections opened by the check | **1**, read-only count |
| lines changed in the guard | **1**, plus a comment |
| new test | production's shape: target set, designation absent |
| new test before the fix | **failed** (returned "allowed") |
| check 7 after the fix | exit 78, "Refusing", no count line, so no connection |
| practice project after the fix | still allowed |

There is only one caller (the retention purge), so the fix covers every
destructive script there is.

The hole was present at least since the guard learned to accept a named hosted
practice project. Any earlier report that recorded check 7 as passing under the
production settings needs re-reading in that light.

---

## 2. What is on production

**The deployment.** `design-pass` was merged into `main`. The `GOAL.md` conflict
was resolved to the `design-pass` version, as D-024 says. The merge commit was
built, the build's commit was confirmed to be the merge commit, and that build
was promoted. Production was then confirmed to serve it. Nothing else was
promoted.

**Placements, checked against the deployed pages.** Each line below was read
from production, not from the source.

| Placement | Where | Result |
|---|---|---|
| Footer statement, verbatim | every page: `/`, `/faq`, `/deadline`, `/feedback`, `/terms`, `/privacy`, `/refunds`, the review route | 8 of 8 |
| Refunds link in the footer | same 8 pages | 8 of 8 |
| `/refunds`, verbatim | new page | HTTP 200 |
| `/terms`, Common Paper cover page | new page | HTTP 200 |
| `/privacy`, facts from code and deployment record | new page | HTTP 200 |
| "Do not upload sealed, classified, or legally restricted material" | upload screen and privacy page | present on both |
| Product statement | top of every scored report, above the score | see section 6 |
| Product statement and short form | report PDF cover, before the score | rendered, test-enforced |
| Deadline sentence, replaced | review route | verbatim |
| Exception sentence, replaced | review route | verbatim |
| "looks identical or your money back" | anywhere | **0** occurrences before and after; nothing to change |
| Acrobat, Adobe, RemeDocs | every public page, deployed | **0** |
| Street address | every public page, deployed | **0**; notice address reads "[address pending]" |

**Crawlers stay closed.** `robots.txt` returns `Disallow: /`, and both `/` and
`/refunds` carry `x-robots-tag: noindex, nofollow`.

**Decision record.** The brief asked for this as D-023. D-023 was already
taken on Oct 30, so the ruling is recorded as **D-025**, worded as the brief
gave it: approved in direction; wording pending owner read; no lawyer at
launch; no insurance at launch; competitor names held.

---

## 3. The Terms page: what the cover page changes

The page is the Common Paper Terms of Service cover page. It incorporates the
Cloud Service Standard Terms v2.1 by link (not reproduced), and is attributed
under CC BY 4.0. The form says cover page modifications control over the
standard terms. **Exactly four standard clauses are modified:**

| Clause | Change | Asked for by |
|---|---|---|
| §1.6 Machine Learning | does not apply; Provider will not train any model on Customer Content; references in §5.6 and §11 removed | **Builder**: owner to keep or strike |
| §5.1 Order Form and Agreement | auto-renewal words deleted; nothing renews | owner ("no automatic renewal") |
| §9.2 Protection by Customer | does not apply; no customer indemnity; no Customer Covered Claims | owner ("remove any customer indemnification") |
| §12.4 Injunctive Relief | "in any court of competent jurisdiction" → "in the Chosen Courts" | **Builder**: owner to keep or strike |

The cover page values are as the brief gave them: Provider, North Carolina law,
Rockingham County courts, the standard's default general cap (1x fees, 12
months), no additional warranties, the provider's IP promise kept as the only
covered claim, and the notice address "[address pending]". Its product
description is the short form plus the "provided as is" paragraph.

**Drafted by the Builder, because the form requires them and the brief did not
give them:** Subscription Period, Cloud Service Fees, Payment Process,
Non-Renewal Notice Period, Order Date. Each is one plain sentence matching the
refund page. All are for the owner to rule on.

The standard terms have no arbitration clause to remove, and the cover page
adds none.

**Exempt by path, not by disabling the rule:** the form's own field label
"Additional Warranties" would trip the widened claim rule. The scan exempts one
file (the Terms page) for one label ("warranty"). Every other banned phrase is
still caught in that file, and "warranty" is still caught everywhere else. A
test asserts the exemption list has exactly that one entry and that removing it
makes the Terms page fail.

---

## 4. The Privacy page: where each fact came from

| Fact on the page | Source |
|---|---|
| Every upload removed **72 hours** after upload, with its report and any email given for it | the retention setting. The page reads the same setting the scheduled purge deletes on, so the two cannot drift |
| Purge runs **hourly** | the scheduled job definition |
| No accounts, so this applies to every document | no owned-document flow exists. The brief's "account documents, 30 days" has nothing to attach to |
| Daily database backups kept **7 days**; they hold the record (file name, page count, score, report, email), **not the PDF** | the database provider's plan and its documentation: file storage is not in database backups |
| Website: Vercel, US. Database and files: Supabase, US. Payment: Stripe | deployment record |
| Checking machine: Railway; **region "[NEEDS BUILDER]"** | could not be confirmed: the host's command line is signed out on this machine |
| Email and mailing list: **Brevo, a French company**; the email address and the file name pass through it | the email code |
| Contact: the one public email address the site already uses | no support address exists |
| "We will tell affected people." | the brief, verbatim |

**This contradicts the brief's premise that the providers are "all in the US".**
The email provider is French. The page says so instead of saying "all in the US".

---

## 5. The claim rule, widened and proven

The rule now blocks, case-insensitively: *compliant, compliance guaranteed,
guarantee, warranty, certified, certifies, passes the ADA, meets the ADA, meets
Section 508, lawsuit proof, fully accessible, 100% accessible*, plus the
phrases it already blocked. A denial in the same sentence ("It does **not
decide** whether… meets the ADA") is allowed through the rule's existing denial
mechanism, which gained the verb "decide". That was needed for the owner's own
product statement. A property name in code (`v.compliant`) is ignored.

The same scan runs under `npm test` and, new this session, under `npm run lint`.
It also now covers the copy modules where the legal wording lives, not only
the page folders.

**The planted violation, verbatim.** A sentence claiming every fixed file is
"100% accessible" was added to the refunds page:

```
claims lint: 1 banned claim(s) in public copy
  app/(marketing)/refunds/page.tsx: 100% accessible -> "...p>{REFUNDS.large}</p> <p>Every fixed file is 100% accessible.</p> </main> ); }"
planted lint exit 1
```

Removed, and run again:

```
after lint exit 0
```

| | |
|---|---|
| tests | **572 passed, 0 failed** (56 files) |
| lint | exit 0 |
| types | exit 0 |
| PDF ordering test, mutated (statement moved below the score) | **failed**, as it should |

---

## 6. Definition of Done checks, by hand

`scripts/verify.sh` **does not exist**. Check 12 (the loop, its Stop hook, the
plant-and-remove proof of the loop) was never installed, so there was no single
judge to run. The checks were run one by one instead.

| # | Check | Result |
|---|---|---|
| 1 | identity | PASS, studio identity |
| 2–4 | lint, types, tests | PASS, 572 tests |
| 5 | canonical document, local | **CANNOT RUN.** The measurement reads from staging storage, which no longer holds the corpus files since staging was pointed at the practice project. It wrote "404" for every document **over the committed results file**. The file was restored from version control at once, and the committed canonical row (42 → 100) is intact. The script's "refuse to shrink" rule did not catch it, because the row count stayed the same |
| 6 | no score without the validator | PASS in substance: exit 2, no number, "No score is produced without the independent validator." The DoD's literal string "validator did not run" is not what the script prints |
| 7 | delete guard refuses live | **FAILED, then fixed** (section 1). PASS after the fix |
| 8 | deployed pages | PASS, 5 of the 6 non-public-path lines, in the run below. The sixth (canonical 42, local) failed only because the clone it ran from lacked the canonical PDF |
| 9 | crawlers closed | PASS |
| 10 | payments in test mode | PASS (checked by prefix, nothing printed) |
| 11 | no internal words on `/`, `/faq`, `/deadline` | PASS. `/privacy` contains "[NEEDS BUILDER]" because the brief requires that marker |
| 12 | loop installed | **NOT INSTALLED** |
| 13 | public path, once | **PENDING**, see below |

**Check 13 so far.** Partway through the session, this machine stopped letting
the Builder's tools read the project folder. To keep going, the deployment
check ran from a fresh clone of `main`. The canonical PDF is a local file, not
in version control, so the clone did not have it. The public-path step failed
at **reading the file, before its first request**:

```
PASS  engine health          HTTP 200 veraPDF pinned 1.30 actual 1.30.2
PASS  auth fails closed      unauthenticated /check -> HTTP 404
FAIL  canonical 42 (local)   validator did not run: … corpus/docs/2026-MAY-5-AGENDA.pdf doesn't exist.
PASS  deployed /             HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /deadline     HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /faq          HTTP 200, superseded dates: false, corrected dates present: true
FAIL  public path            ENOENT: no such file or directory, open 'corpus/docs/2026-MAY-5-AGENDA.pdf'
```

**No live row was created.** The one-row allowance is unspent. A second attempt
from the real folder failed before the script started, also with no row. Check
13 runs once when folder access returns, and this report is updated with its
output. The token goes to the private corpus only.

---

## 7. A defect found in passing, fixed

**The footer links were unreadable on the dark footer.** The link color was
inherited from the page's dark body text, so on the dark band the links read as
bare underlines. That was already so before this session, and was found when
Refunds joined the row. The links are now 85% white, measured at **13.2:1**
against the footer's lightest stop. Screenshots below are after the fix.

---

## 8. What the brief asked for that the product cannot carry yet

| Asked for | Why it is not there | Where it went instead |
|---|---|---|
| Product statement "beside the Fix button" | there is no Fix button | top of every scored report, and the report PDF cover. A comment marks where it goes when the button ships |
| Short form on the "pricing page" | there is no pricing page | report PDF cover, and the Terms cover page |
| Refunds link "from the checkout step" | there is no checkout step a buyer reaches | footer, and the Terms cover page |
| Refund page: "Before you pay, you see the fixed document" | the payment code authorizes, checks scope, then captures. The buyer previews nothing before capture | page published verbatim as instructed. **The page describes a flow the product does not have yet.** Owner to rule |
| Product statement on a *declined* document's page | a decline has no score and no report | not placed. The decline screen keeps its existing framing line |

---

## 9. For the owner

1. **The refund page promises a preview before payment that the product does
   not offer.** Rule on the wording, or on building the preview.
2. Monthly plans "do not renew" (refunds) and "no automatic renewal" (terms)
   agree. Confirm that is the plan, since a monthly plan that never renews is
   unusual.
3. The two Builder-added clause changes (§1.6, §12.4): keep or strike.
4. The five drafted cover page values (section 3).
5. Rockingham County has no federal court seat. "State, federal, or otherwise"
   is the form's wording; in practice it means the county's state courts.
6. A support address. The site uses its one existing public address.
7. On the review route, the replaced deadline sentence follows "It extended the
   original deadlines by one year." The two now say the same thing twice.
8. The old non-affiliation line ("a private tool from Archipelago International,
   Inc., not affiliated with any government agency") is still on the report
   PDF's last lines, the report-link email, and the structured data. It is true
   and does not conflict. The brief put the new statement on the site's pages,
   so the other surfaces were left for the owner's read.
9. A language model is configured, but no deployed route sends a document to
   it. Before fixing ships, the privacy page must say whether one does.

**For the Builder, next session:** confirm the checking machine's region (the
host's command line needs signing in on this machine); install
`scripts/verify.sh` and check 12; make the measurement script refuse to
overwrite results with 404 rows; run check 13 if this session could not.

---

## 10. Screenshots

All taken after the footer fix, at 1280 desktop and 390 mobile. Reduced-motion
was also captured; it renders the same content.

- [Terms, desktop](2026-11-01-legal-posture/terms-desktop-1280.png)
- [Privacy, desktop](2026-11-01-legal-posture/privacy-desktop-1280.png) ·
  [Privacy, 390](2026-11-01-legal-posture/privacy-mobile-390.png)
- [Refunds and footer, desktop](2026-11-01-legal-posture/refunds-desktop-1280.png) ·
  [Refunds and footer, 390](2026-11-01-legal-posture/refunds-mobile-390.png)
- [Upload screen line, 390](2026-11-01-legal-posture/upload-line-mobile-390.png)
- [Report PDF cover: statement and short form before the score](2026-11-01-legal-posture/report-pdf-cover.png)
  (generated from a sample input, no real document)
- Report page statement on production: **pending check 13.** It is
  photographed from that run's single row, so no extra row is created.

---

## Live database

**Rows created: 0** at filing. **Connections opened: 1**, the read-only count
described in section 1. Nothing was deleted, nothing was charged.
