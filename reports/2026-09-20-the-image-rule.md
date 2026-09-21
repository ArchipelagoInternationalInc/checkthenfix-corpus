# The image rule, built
## 2026-09-20 · Builder session · D-021

Recorded as **D-021** in DECISIONS.md in the Executive Director's words. Built,
proven, and one thing it costs is stated plainly at the end.

## What the rule replaces

Two failures, one on each side of the same decision, both found in the competitor
comparison:

- **Ours:** a seal was artifacted because nothing had looked at it. `decorative`
  defaulted to true, the image pass had never run, and the result was
  indistinguishable from a considered decision.
- **Theirs:** the same seal described in 424 fluent characters, with five facts
  wrong including the entity's own name.

A silent omission and a confident error. The rule closes both.

## 1. Silence is no longer decorative

Every image must reach the engine with `examined: true` and an explicit decision.
An image with no entry, or an entry that does not say it was examined, **declines
the document**. The old "refuse to bluff" branch — examined, but neither
described nor marked decorative — is unchanged.

The test the rule asked for runs the **real engine**, not a stand-in, because the
default that caused the problem lived in the engine:

```
empty plan, the shape that used to succeed by default
  exit code: 3  (3 = declined)
  {"declined": "page 1: 1 image(s) reached the engine with no recorded decision
                — D-021, silence is not decorative"}
  no file produced — correct

the same document with the image examined and decided
  exit code: 0  (0 = ok)
  file produced — correct
```

## 2. Descriptions are checked against the image

Whatever the judgment layer proposes is verified against text recognised **on the
image itself**. Numbers and words that behave like names must appear there.
Ordinary descriptive words — "circular", "shield", "book" — are **not** checked:
they are visual observations rather than legible text, and demanding they appear
in a recognition result would reject every true description of a picture.

**Matching is exact — no tolerance for recognition noise — and that is the whole
design.** The misspelling this rule exists to catch is one character from the
correct name; the wrong year is one character from the right one. Any tolerance
generous enough to absorb noise is generous enough to accept precisely these
errors. When recognition cannot confirm a claim, the claim is not confirmed.

A refused description falls through to decorative **with the reason recorded**, so
a report can say a description was proposed and refused rather than implying
nobody tried.

### The cap: 125 characters

Config-driven (`MAX_IMAGE_DESCRIPTION_CHARS`), defaulting to **125**.

Why that number: it is the figure assistive-technology guidance has settled on —
several screen readers historically truncated alt text around there, and the
mainstream style guides recommend staying under it. It is also a useful forcing
function: a description that will not fit is usually describing more than a
reader needs, or has drifted from describing into interpreting. For scale, the
competitor's description of the same image was **424 characters**, three and a
half times the cap, and four of its facts were wrong.

## 3. The canonical document: **decorative, with a note**

No description was published — **and not because anything judged the image
decorative.** The recogniser could not read it.

Eight attempts: four page-segmentation modes, on both a greyscale upscale and a
binarised version. **Zero of nine expected keywords recognised**, every time. The
text on this image runs around a circle, and standard recognition reads
horizontal lines. No tuning fixes that.

A true, 62-character description was proposed and refused:

```
OUTCOME: DECORATIVE
note: examined; a description was proposed and refused
      (the image does not visibly contain: <five claims, named in the note>)
```

The description was accurate. The engine could not confirm it was accurate, and
the rule does not let it publish an unconfirmed claim. **Failing towards silence
is the direction the rule chooses.** The description and the image's contents are
in the private corpus, since both name the entity.

## 4. The rejection, proven — and the check is not merely refusing everything

Against the image's own words supplied as the reference:

| Description | Result |
|---|---|
| the true one, 62 characters | **ACCEPTED** — five claims checked and found |
| one with a wrong name and two wrong dates | **REFUSED** — four claims named as not visibly contained |

Both wrong values are a single character from the correct ones. Both were caught.

**Mutation-tested four ways**, all caught:

| Mutation | Tests failed |
|---|---|
| always accept | **7** |
| stop extracting claims to check | **6** |
| allow a one-character difference (the "recognition noise" temptation) | **5** |
| substring matching instead of whole-token | 1 |
| drop the length cap | 1 |

## 5. The corpus re-run

**The canonical document is unchanged: 42 before, 100 after.** The rule cost
nothing on the document it was designed around. The re-run also reproduces every
filed measurement exactly — 0% of pixels changed, 1,810 characters, 123 structure
elements — so the comparison table in the competitor report stands as published.

**One result changed, and it is the rule working.** The engine's integration test
ran the engine with **no plan at all** and passed *because of the default the rule
removes*. It now supplies an explicit decision, and a companion test asserts the
decline — so the rule cannot be quietly lost by someone making the first test
pass again.

No document's score changed. What changed is what happens when nobody has looked.

## Checks

Lint clean, types clean, **486 tests pass** (468 before, 18 added).

## The cost, stated plainly

**28 of the 30 corpus documents carry at least one image.** Under this rule every
one of them declines unless an image decision is recorded for each image. That is
the intended behaviour and it is also a real operating cost: the image pass now
has to run, on documents where it previously did not, or the document does not
complete.

And the canonical document shows the sharper edge. Circular seals sit on the
letterhead of a great many public documents, and under this rule the engine will
**artifact every one of them** rather than describe them — however good a
description a model produces — because nothing can confirm the words. A reader
gets silence where the entity's own name is printed.

That is the correct outcome under the rule as written, and it is better than the
alternative on display in the competitor comparison, where the description is
published and the town's name is wrong. But it is not a good outcome, and the
thing that would lift it is recognition that can read curved text — unwrapping
the ring into a line before reading it. Worth knowing before anyone reads
"decorative" in a report as meaning "we looked and there was nothing to say."

## Not in this report

No entity name, nothing of the image's contents, no document contents. The
canonical proof, the recognition input, and the descriptions are in the private
corpus under `image-rule/2026-09-20/`.
