# Slices C, D, E — email wall removed, claims guard, marketing shell

**Date:** 2026-08-19 · 281 tests, lint, typecheck and production build clean.

---

## Slice C — email-optional sample lane, and three ratified rulings

### Rulings applied

| Ruling | Result |
|---|---|
| Page title | `CheckThenFix: Free PDF accessibility checker` |
| Progress line | "You can leave this page. Your report stays at this link." |
| Staged CTA | Published; flag and superseded sentence deleted together |

All three replacements happen to carry no dash, so the dash backlog emptied.

### The email wall is gone

`/api/check/queue` used to require an address before it would do anything,
because the only way a user learned the result was the email. That stopped being
true once the link became persistent and the progress screen said so — which
made the form a contact detail collected in exchange for a service we can render
without it.

Now `{token}` starts the check and `{token, email}` also attaches an address.
One route, because the start is idempotent.

The email provider no longer gates the route. It gates only the **promise**:
with no provider configured an address is refused rather than stored against a
delivery that will never happen.

### Two protections the form was providing by accident

**`startQueuedCheck` had no idempotency guard.** It downloaded and checked
unconditionally. Survivable while the only way in was submitting a form;
replacing the form with a button means one token can be spent as fast as someone
can click, and each click is a full engine run. This is the hole found and closed
on `/api/check`, arriving again through a different door. The guard now lives in
`startQueuedCheck`, so it holds for every caller.

**The route was not rate limited.** The limiter on `/api/upload/start` caps how
many tokens a caller can *obtain*, not how many may be *spent* here.

**Retention verified:** the address lives on the document row and goes with the
cascade; the sample object is reachable by the purge only because its path is
derived from the source's. Both asserted, plus the limiter exercised rather than
described.

### Blocked

The ruling approved the partial-check strings *"with the two PM smoothings"* —
but the smoothed text did not reach the Builder. Shipping the drafts as approved
would publish wording the owner did not write, so the three partial strings
remain marked unapproved. **This is decision #1.**

---

## Slice D — banned-claims guard

Same machinery as the dash rule, one rule apart: **no allowlist**. A dash in
approved copy is awaiting a rewrite; there is no approved use of "we guarantee
compliance". A test asserts no allowlist structure exists.

### The hard part

The approved FAQ answer is the most important sentence on the site and it
contains two banned phrases:

> "Will this make us ADA compliant? No tool can promise that … anyone
> guaranteeing it is overclaiming."

A guard that fires on it is a guard that gets deleted.

**Mechanism considered and rejected — quote-gating.** Allow a banned phrase only
inside quotation marks, on the use/mention distinction. Reads well, fails
immediately: the approved copy does not quote either phrase. It asks a question
and then denies it. Quote-gating would have forced a rewrite of owner-approved
copy to satisfy a lint.

**Mechanism used — denial proximity, two forms.** Either the phrase's own
sentence carries a denial marker, or its sentence is a **question** and the next
sentence denies it. The second form exists because a FAQ heading is a question in
the reader's voice and the denial lands in the answer. Either form alone rejects
half the approved copy.

Markers are specific constructions ("no tool", "cannot", "does not claim",
"overclaiming") rather than bare "not", which appears in a large share of
ordinary sentences.

**What it cannot do,** stated in the module: a denial marker in the same sentence
as a genuine promise defeats it. A sentence that both promises and denies is a
human-review problem. The guard catches accidents and careless copying.

### Mutation results

All eight phrases planted and fired:

| Phrase | Fires |
|---|---|
| ADA compliant (as a promise) | ✓ |
| guarantee | ✓ |
| certified | ✓ |
| lawsuit-proof | ✓ |
| fully compliant | ✓ |
| one line of code | ✓ |
| automatic full compliance | ✓ |
| effortless | ✓ |

Plus: a test asserts the plant list covers `BANNED_CLAIMS` entry for entry;
planting "We guarantee your document will be fully compliant" in the real
dropzone fails the suite and names the file and both phrases; a question **not**
answered with a denial still fires; a denial two sentences after a promise does
not rescue it; and the scan is verified non-hollow (>8 files, >20,000 chars) so a
renamed route group cannot let it pass by reading nothing.

Current user-facing copy: **zero violations**.

---

## Slice E — marketing shell

Five routes scaffolded. The footer is in the root layout, so the non-affiliation
statement is on every page by construction — verified in the browser on each
shell page.

| Route | State |
|---|---|
| `/faq` | **published** — four approved bank entries, verbatim |
| `/deadline` | **published** — approved deadline sentence + primary sources |
| `/terms` | 404 — no approved copy |
| `/privacy` | 404 — no approved copy |
| `/feedback` | 404 — no approved copy |

The three unpublished pages 404 rather than rendering a heading over an empty
page. A legal page that exists and says nothing is worse than one that does not
exist, and it is the page a reader reaches when they most need a straight answer.
One registry holds all five flags. Tests assert the unpublished pages contain no
heading and no stray sentence at all.

**Held copy is specified, not drafted.** For the sample lane, drafting proposed
wording was useful — it is product copy and the owner edits tone. For legal text
and an explainer about a federal rule, a Builder draft is a plausible-looking
document someone might approve without the scrutiny it needs.

The privacy entry is the exception: its facts are already true in the code and
are stated precisely, because they are ours to state.

### A real hole in the dash guard, found by shipping the FAQ

The guard matched the literal em and en dash **characters**. JSX here routinely
writes punctuation as HTML **entities**, and an entity is not the character — so
every dash written as `&mdash;` had been passing silently.

It surfaced because adding the FAQ's two dashes to the backlog *changed nothing*:
the guard had never seen them.

Both guards now decode entities through one shared helper. The same blind spot
cut the other way in the claims guard, whose denial markers include `can't` and
would never have matched `can&rsquo;t` — a legitimate disclaimer written with
entities would have been reported as a banned claim.

**Dash backlog 0 → 2.** Both are approved bank copy predating the rule, and the
rule's standing instruction is that such copy ships while the rewrite is the
owner's. Both entries verified load-bearing: removing them fails the suite.
