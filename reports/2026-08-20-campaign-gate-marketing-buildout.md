# Campaign gate — marketing build-out: content, SEO/AEO, email capture

**Date:** 2026-08-20 · **Branch:** `design-pass` · Slices A–D, run consecutively.
**At gate:** 332 tests, lint, typecheck and production build clean; 12 captures console clean.

> **This is a real read.** Slice A is a full proposed-copy set — a deadline
> explainer, eleven FAQ entries, a samples gallery, a state template and a
> checklist. Nothing in it has shipped. Budget a sitting.

---

## Read this first

**Nothing from Slice A is live.** All of it is drafted in `lib/copy/proposed.ts`
and its four routes 404. **Slice B ships** (metadata, schema, sitemap, robots,
llms.txt). **Slice C ships the notice line only** — the list wiring is built and
inert. **Slice D is documentation.**

Carried from round 6.6 and still true: **every Vercel deployment since round 5b
is `BLOCKED`**, so none of this is viewable anywhere but a local dev server.

---

## Slice A — content drafted, four routes scaffolded

Held in `lib/copy/proposed.ts`, in **one file** rather than a
`lib/copy/proposed/` directory: the quarantine guard matches imports of
`copy/proposed`, and a subdirectory would slip past it. A long file is a smaller
problem than a hole in the guard.

**Drafted:** `/deadline` explainer body · full FAQ (4 approved entries marked as
such, **7 drafts** for the gaps) · samples gallery · state template + 2 pilots ·
checklist lead magnet.

### Before/after scores are measured, not written

CLAIMS §7 forbids fabricated scores, and a gallery is exactly where a plausible
"after" gets invented. Both halves of every pair were checked on the engine:

| Document | Before | After |
|---|---|---|
| Board agenda, 2015 | 38 | **95** |
| Council agenda, April 2026 | 42 | **100** |
| Council agenda package, Dec 2025 | 42 | **85** |
| Council agenda package, Mar 2024 | 47 | **90** |

All government-produced, from our own corpus, as §7 requires. Near-identical
packages deduplicated by score pair. `scripts/measure-before-after.ts`
reproduces it.

*The first run returned seven nulls: the engine returns the score as a sibling
of `report`, not inside it, and reading the wrong field failed silently.*

### The state template carries no state law

The federal rule applies everywhere and the deadline turns on the entity's
**population**, not its state. So a state page can honestly restate the federal
rule and its sources — and nothing more. A state's own accessibility policy is
the part that would make these worth reading, and the part a Builder must not
invent. `statePolicy` is **null** on both pilots.

Publishing fifty pages of restated federal content would also be thin duplicate
content — an SEO liability, not a win.

---

## Slice B — SEO/AEO, measured on a production build

### The checklist

| Item | State |
|---|---|
| Per-page metadata + OpenGraph | ✅ homepage, /faq, /deadline, /feedback |
| Canonical URLs | ✅ + `metadataBase` at root |
| Unique H1 per page | ✅ |
| JSON-LD: Organization, SoftwareApplication, HowTo | ✅ homepage |
| JSON-LD: FAQPage | ✅ /faq, four approved entries |
| XML sitemap | ✅ derived from the published registry |
| robots.txt | ✅ disallows `/r/`, `/api/`, `/dev/` |
| `llms.txt` | ✅ 389 words |
| Image alt | ✅ **nothing to fix — see below** |

### Structured data asserts nothing we cannot back

A reader sees the disclaimer beside a sentence; a crawler consuming JSON-LD does
not. So:

- **no `offers`** — pricing is not set, and an invented or zero-price offer is a
  false claim to a consumer that cannot see the page saying otherwise
- **no `aggregateRating`** — we have no reviews; a fabricated rating is the
  single most common structured-data lie
- **no logo** — we ship no logo asset
- **non-affiliation stated** in the Organization description

The payloads run through the claims and dated-claims guards, because they are
copy. FAQ schema is the four approved entries; a test asserts every schema
question appears in the rendered page.

### Image alt: nothing to fix, and why

The marketing surface ships **zero raster images**. Every graphic is inline SVG,
decorative, already `aria-hidden`. Lighthouse scores accessibility 100 with 42
audits *not applicable* for exactly this reason.

### Lighthouse — production build, not the dev server

| Page | Perf | A11y | Best practices | SEO |
|---|---|---|---|---|
| `/` | **96** | **100** | **100** | **100** |
| `/faq` | **98** | **100** | **100** | **100** |
| `/deadline` | **96** | **100** | **100** | **100** |

FCP 0.8s · LCP 2.8s · TBT 0ms · CLS 0 · Speed Index 1.3s

The four performance points are LCP (0.84) and back/forward cache restoration.
**Nothing was chased**, because chasing LCP means touching ratified motion.

Lighthouse was installed, run and uninstalled — the font-package pattern.

**Homepage wording untouched.**

---

## Slice C — email capture

### Shipped

The notice, **verbatim**, at both places an address is collected today: the
report page and the big-document form.

It is **one exported constant**, and a test asserts the literal appears in
neither component. Two copies would be two different things subscribers had
agreed to. It renders **before the field**, not after the button — it is the
consent basis, so it must be readable before an address is typed.

### Brevo wiring — built and inert

**Fails closed by design.** Nobody joins a list unless `BREVO_API_KEY`,
`BREVO_LIST_ID` **and** `BREVO_DOI_TEMPLATE_ID` are all set. None are.

**There is no single-opt-in fallback, deliberately.** A plain `POST /v3/contacts`
adds the address immediately — single opt-in wearing a double opt-in's name. The
only path is Brevo's `doubleOptinConfirmation`, which adds nobody until they
click. A test asserts the fallback does not exist.

| Setting | Value |
|---|---|
| Opt-in | Double, via `doubleOptinConfirmation` |
| Tracking | Unchanged — nothing enables open/click tracking |
| Data sent | Address + a source label. No filename, score or token |
| Failure mode | `after()`, never throws, cannot affect the response |
| Unsubscribe | Brevo list-managed; the notice states it |

**Configuration the owner must supply:** `BREVO_LIST_ID`,
`BREVO_DOI_TEMPLATE_ID`, and optionally `BREVO_DOI_REDIRECT_URL`.

### No email is ever required — guard-tested

The sharpest failure mode in the campaign: a field that quietly becomes mandatory
turns a free tool into a lead-capture form, and nothing about the page looks
different. Asserted structurally — `/api/check` reads only a token from the
request, the queue route keeps its address branch conditional, the report renders
from a token, and the free upload surface has no email input.

*The first version of that test banned the word "email" from the check route and
failed on a legitimate use — the route consults `emailConfigured()` because
big-document mode only offers itself when it can keep its promise. A rule about
what the route **reads**, written as a rule about what the file **mentions**.*

### Staged, not built

The deadline-updates signup. Both host surfaces are either already published
(where an unapproved box would be live copy) or unpublished (where nobody could
review it), so there is nowhere to stage it in place.

---

## Slice D — guest checkout, spec only

**D-017** written to `DECISIONS.md`, cross-referenced from the GTM doc. No Stripe
work.

One-time purchases require no account; membership is pitched post-purchase and on
the quote lane; membership is never a gate on a one-time purchase.

One thing is deliberately **not** settled: retention for a paid guest document is
a separate decision from the anonymous 72-hour window.

---

## Delegation log

| # | Call |
|---|---|
| 1 | Proposed copy kept in one file, not a directory, so the quarantine guard keeps matching. |
| 2 | Before/after scores measured on the engine rather than drafted; script kept. |
| 3 | State template ships no state law; `statePolicy` null pending owner citations. |
| 4 | Four Slice-A routes scaffolded as 404s, the ratified `/terms` pattern. |
| 5 | `llms.txt` reworded ("cannot make") rather than widening the claims guard's denial vocabulary to admit "does not make". |
| 6 | JSON-LD omits `offers`, `aggregateRating` and `logo` rather than filling them. |
| 7 | Sitemap derived from the published registry rather than hand-written. |
| 8 | `robots` disallows `/r/` as a privacy measure, not an SEO one. |
| 9 | Newsletter notice as a single constant across two client components, via `lib/copy/newsletter.ts`. |
| 10 | No single-opt-in fallback in the Brevo layer. |
| 11 | Subscription in `after()`, isolated from the response. |
| 12 | Lighthouse run against a production build and then uninstalled. |

---

## Decisions for the owner

1. **The full proposed-copy set** — deadline explainer, 7 FAQ drafts, samples
   gallery, state template, checklist. This is the sitting.
2. **Pricing FAQ** is a placeholder; nothing was drafted.
3. **State pages**: supply per-state citations, or drop the state template.
4. **Brevo**: list ID and DOI template ID.
5. **D-017 guest checkout**: confirm, plus retention for paid guest documents and
   whether guests can later claim purchases into an account.
6. **The blocked deployments** — still nothing viewable since round 5b.
7. Carried: label colour and weight, the IFR permalink, the mobile CTA,
   `/terms` and `/privacy`.
