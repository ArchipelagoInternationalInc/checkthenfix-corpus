# The deploy was one wrong setting. Then the owner's corrections, and the nav button.
## 2026-09-25 · Builder session

The engine deploys again. The cause was a single setting on the container host,
and it had nothing to do with the code.

Everything the last report listed as blocked is now done. Two sentences in the
copy need counsel before launch — **section 6**, and that is the one item in
this report that needs someone other than the Builder.

---

## 1. Why every deploy failed

**A path belonging to this Mac was sitting in the container's settings.**

The engine's container is built with the validator installed inside it, and the
image sets the validator's location to a path inside the image. The container
host's own settings carried a variable with the same name, set to a path on the
hub Mac — `/Users/...`, a directory that does not exist inside a container.

A host variable beats an image's baked-in one. So every container started,
looked for the validator at a Mac path, found nothing, and its health endpoint
answered 503 by design: the endpoint's whole job is to refuse when the pinned
validator is not there. The host waited for a healthy answer, never got one,
and marked the deploy failed. Then it restarted the container and the same
thing happened again.

The service was behaving correctly at every step. A health check that returns
503 when the validator is missing is the check working, not the check failing.

**How it was found — and how it was not.** The failed deployment's health-check
log could not be retrieved: the host's log command kept returning the last
*successful* build's output rather than the failed deployment's. Rather than
keep pulling the same cached log, the settings were read directly and compared
against what the image sets. One variable disagreed. Deleting it was the whole
fix.

| | |
|---|---|
| deploys attempted before the fix | 2, both failed |
| deploys after the fix | 1, **succeeded** |
| lines of code changed | **0** |
| settings changed | **1** (one variable deleted) |

**What made this slow to see:** the failure looked like a startup or port
problem, because the symptom was "the container starts and then the host gives
up on it". The container was starting fine. It was answering honestly that it
was not ready, and the honest answer is indistinguishable from a crash if you
only watch the deploy status.

---

## 2. The deployment check, every line

Run against production after the deploy succeeded. This is the complete output,
not a summary of it.

```
PASS  engine health          HTTP 200 veraPDF pinned 1.30 actual 1.30.2
PASS  auth fails closed      unauthenticated /check -> HTTP 404
PASS  canonical 42 (local)   May agenda scored 42 (expected 42)
PASS  deployed /             HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /deadline     HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /faq          HTTP 200, superseded dates: false, corrected dates present: true
PASS  public path            scored 42 · token <withheld> (one anonymous row, self-removing)

TRIPLE GREEN
```

**The public-path line is the one that matters.** It is the only step that
proves the deployed product rather than the deployed pages: a real document
goes in through the public upload route, the deployed engine scores it, and the
report page is read back. It scored **42**, the number reproduced across the
CLI, a direct engine call and a deployed preview since August.

The token is withheld here deliberately. An anonymous report token is a bearer
credential — anyone holding it can open that report page — so it goes to the
private record and never into this public one. It is the same reason report
URLs stay permanently closed to crawlers.

### Live rows created, and how they go away

Proving the public path writes a row to the live database. That is the one
named exception to "nothing touches the live database", and its terms include
always reporting it.

**Four rows this session**, each from one anonymous upload, nothing charged.
One from the run immediately after the deploy succeeded; three more from this
closing pass, which re-ran the check to capture the output above verbatim and
then to confirm a fix to the check itself. Each row is left to the ordinary
anonymous retention window, which removes it without anyone acting.

Three runs to capture one output is more rows than the exception should need.
Noting it rather than excusing it: the rule keeps its meaning by being narrow,
and "I needed a clean transcript" is exactly the sort of convenience it is
supposed to refuse.

---

## 3. A defect the check itself was hiding

Running the check under a configuration that does not define the validator's
location produced this:

```
FAIL  canonical 42 (local)   May agenda scored 57 (expected 42)
```

**57 is not a score.** The validator never started. The adapter had done its
job — it reported itself unavailable and substituted no result — but the check
scored the report anyway, and the structural checks alone add up to 57.

It failed, so no false number was ever published. It failed **with the wrong
reason**, which sends the next person to investigate a document that did not
change.

Fixed. The step now refuses:

```
FAIL  canonical 42 (local)   validator did not run: veraPDF binary not found ... No result is substituted.
```

This is the product's own rule applied to the product's own tooling: a score is
computed from validator output, or it is not reported. Two assertions guard it,
because the first alone would only check where a line sits in a file, and
position is not behaviour.

---

## 4. The owner's copy corrections

Applied to the staged copy, the review page, and every live surface carrying
the same sentences. The FAQ carried several of them already.

### 4.1 The deadline note — **needs counsel, see section 6**

> **Before:** "The Department has said it may revise the rule further. Confirm
> current dates at ADA.gov. The extension moved the technical deadline. It did
> not suspend existing ADA obligations, which apply now."

> **After:** "The Department may revise the rule again. Nothing was suspended.
> Today's obligations still apply. Confirm current dates at ADA.gov."

### 4.2 The exception sentence — **needs counsel, see section 6**

> **Before:** "Even where an exception applies, other ADA obligations,
> including effective communication, can still apply."

> **After:** "An exception narrows one requirement. It does not lift the
> others. Effective communication still applies."

### 4.3 The product line

> **Before:** "CheckThenFix finds structural problems in PDFs, fixes the ones
> automation can fix, records the results reported by a named validator, and
> flags what needs human review."

> **After:** "CheckThenFix finds the structural problems in your PDF. It fixes
> what automation can fix. It records what veraPDF found, by name. It flags the
> rest for a person to review."

The validator is now named in the sentence itself rather than referred to as
"a named validator". That is a stronger claim and a true one: the version is
pinned and the check above reads it back from the running engine.

### 4.4 The verification line

> **Before:** "Verification happens in two places. veraPDF, the named
> validator, produces the automated results, and your reviewer confirms the
> items flagged for human judgment."

> **After:** "Two checks stand behind the result. veraPDF produces the machine
> findings. A reviewer on your side confirms the items we flagged for
> judgment."

### 4.5 Long sentences broken up

Every public sentence was swept for three and four clauses carried on commas.
Changed:

| where | what it was | what it is |
|---|---|---|
| home, deadline band | one sentence carrying both deadlines and the extension | three sentences |
| home, answer paragraph | "reports it in plain language: what passes, what fails, and what each failure means…" | three sentences |
| home, the fix paragraph | one sentence, four clauses | three sentences |
| FAQ, limits answer | the product line above, one sentence of four clauses | four sentences |
| FAQ, scanned-PDF answer | "is not automatic: an older PDF generally…" | split at the colon |
| FAQ, verification answer | "Automated results do not replace a manual review; check the flagged items…" | split at the semicolon |

**Not changed, deliberately:** the long lines on the deadline page are Federal
Register citation titles. Breaking a citation would misquote it.

### 4.6 Internal vocabulary removed

A section of A-B colour and weight comparisons — with contrast figures and
"shipped" notes — was sitting on a page the owner reads as customer copy. It is
working text and it is gone, along with the word "Builder" from that page's
framing line.

The live pages were then swept for round numbers, report names and build
vocabulary. **None reaches rendered copy.** The remaining matches are code
comments, which are the codebase's memory of why a thing is the way it is, and
those stay.

---

## 5. Setting the product name in bold, with no markdown renderer

The obvious move was to put `**CheckThenFix**` in the copy strings. It would
have shipped literal asterisks: nothing in this application renders markdown.

There is a second reason it was the wrong shape. **The FAQ carries every answer
twice** — once for a reader, once as structured data for search engines. An
asterisk or a tag inside structured data is not emphasis; it is a defect
visible to every consumer of that feed.

So the strings stay plain and the emphasis happens at render time: one helper
splits a string on the product's name and wraps the matches. The reader sees
bold, the structured data stays clean, and the name is spelled in exactly one
place in the codebase.

Six tests cover it, including one asserting the structured data stays plain.

**Two of the Builder's own mistakes were caught on the way and are recorded
because they are the kind that repeat:**

1. An HTML entity (`&rsquo;`) was moved into a JavaScript string, where it
   renders as five literal characters rather than an apostrophe.
2. The first version of the test was written with a `.tsx` extension, which the
   suite does not collect. **A test that cannot fail.** It sat green and proved
   nothing.

Both now have assertions.

---

## 6. FLAGGED FOR COUNSEL — two sentences changed legal meaning

Both sentences in **4.1** and **4.2** are live on the site now, at the owner's
instruction. Both say something different from what they replaced, and both are
statements about what the law requires.

**The deadline note.** "Nothing was suspended" is a broader and flatter claim
than "it did not suspend existing ADA obligations". The old sentence said one
specific thing was not suspended. The new one says nothing at all was.

**The exception sentence.** "An exception narrows one requirement. It does not
lift the others." states a general rule about how the exceptions operate. The
old sentence was hedged — "other ADA obligations … *can* still apply". The new
one is unhedged.

Both readings may well be correct. **Neither was confirmed by anyone qualified
to confirm it**, and the Builder is not qualified to. They should be read by
counsel before launch.

Two things reduce the exposure in the meantime: both sentences still sit beside
"Confirm current dates at ADA.gov" and "Obtain legal advice for a fact-specific
determination", and the site is still closed to search engines, so neither
sentence is indexed anywhere.

---

## 7. The nav button, and the standard we sell

The owner asked for the logo's teal on the top navigation button, sampled from
the logo rather than assumed to match an existing colour.

**Measured, not assumed.** The colour was read by counting pixels in the
rendered wordmark: **22,959 pixels of `#339F96`**, and 43,246 of the ink
`#231F20`. The site's existing accent does not appear in the mark at all. They
were never the same colour.

**The first version failed and was not shipped.** Filled with the teal and
labelled in white, the button measured **3.21:1**. WCAG 2.1 AA asks 4.5:1 of
normal text, and this label is 15px at regular weight. So the teal kept its
exact value and the *text* changed, to the logo's own ink, at 5.08:1.

A finding worth writing down, because it is the opposite of the instinct: with
dark text on a mid-tone, **the hover had to go lighter**. Every darker step
measured *below* 4.5:1. Darkening closes the gap instead of opening it.

**Then the owner asked for an outline instead, and the outline is what makes
the colour usable.** `#339F96` cannot carry text in either direction — as teal
text on white it is 3.21:1, and as a ground under white text it is the same
3.21:1. Both fail. But an outline is judged under a different rule: WCAG 2.1
1.4.11 asks **3:1** of a control's visual boundary, not 4.5:1.

| what | foreground | background | ratio | bar | |
|---|---|---|---|---|---|
| outline vs header | `#339F96` | `#FFFFFF` | **3.21:1** | 3:1 | PASS |
| label, resting | `#231F20` | `#FFFFFF` | **16.3:1** | 4.5:1 | PASS |
| label, hovered | `#231F20` | `#339F96` fill | **5.08:1** | 4.5:1 | PASS |
| *teal as text* | `#339F96` | `#FFFFFF` | *3.21:1* | 4.5:1 | *would FAIL* |
| *white on teal* | `#FFFFFF` | `#339F96` | *3.21:1* | 4.5:1 | *would FAIL* |

Every ratio was read off computed style in the running page, not calculated
from what the stylesheet was meant to say. Checked at desktop width and at
390px, resting and hovered, with the hover frames produced by moving a real
pointer onto the button so the captures prove the stylesheet rather than an
inline style.

The colour lives in the shared token file, not in the component.

**A guard test now asserts all five rows**, including the two that would fail.
A contrast guard that only checks the passing case would pass just as happily
on a button coloured wrong. Nothing on this site should fail the standard the
product checks other people's documents against, and now nothing can regress
into failing it quietly.

---

## 8. A correction to something the Builder reported earlier today

Earlier in this session the Builder reported that one integration test could
not pass because **there was no Java runtime on this machine**, and that the
validator therefore could not start.

**That was wrong.** The runtime was installed, and so was the small launcher
that exists precisely to make it findable — both set up weeks ago and both
recorded in the handoff. The test was run with a hand-guessed path to the raw
validator instead of the launcher the project configures. The tooling was fine;
the invocation was not.

The suite is **541 tests, all passing**, run the way the repository intends.

This is recorded because the failure mode is worth naming: an environment
problem was diagnosed from a single error message without checking the
project's own notes on that exact error. The handoff already had the answer
under a heading that names it.

---

## 9. State at the end of the session

| | |
|---|---|
| engine deploys | **succeeding** |
| deployment check | **TRIPLE GREEN**, public path scoring 42 |
| tests | **541 pass, 0 fail** |
| lint / typecheck | clean |
| owner's copy corrections | **applied**, staged and live |
| nav button | **shipped**, every state measured and guarded |
| two legal sentences | **LIVE, awaiting counsel** |
| site visible to search engines | **no**, still closed |

### Open

1. **Counsel reads the two sentences in section 6.** The only item here that
   needs someone other than the Builder.
2. The real domain, and the owner's approval of the copy. Search engines stay
   closed until **both**, not either.
