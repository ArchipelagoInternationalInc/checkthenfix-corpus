# Reading round seals
## 2026-09-21 · Builder session · follow-up to D-021; the rule is unchanged

The image rule was right and the reading was broken. Recognition reads
horizontal lines; a seal puts its words around a ring. On the canonical document
that meant none of nine expected words were recovered, so a description that was
actually correct could not be confirmed and the seal was artifacted.

That is now fixed, and **not one line of the checking changed.**

## How it works

Sample the image in polar coordinates and write the result as a rectangle — each
row a radius, each column an angle. The ring comes off like a label off a jar
and the text lands straight.

Four things it had to get right, each learned from a real seal rather than
guessed:

- **Three radial bands.** Seals carry an outer ring and usually an inner one, and
  the outer ring sits at different radii on different seals. Two bands read the
  canonical document and missed four others whose lettering sits further out
  inside a dotted edge.
- **The bottom of a ring is upside down once flattened.** The fix is to rotate
  the strip 180°, not to sample the other way round — sampling backwards only
  mirrors the glyphs, which reads no better than the curve did.
- **The seam.** Unwrapping cuts the ring somewhere and destroys whatever word
  sits on the cut. The first attempt lost a word's first letter that way. Four
  start angles fix it: a word broken by one cut is whole in another.
- **Resolution.** The image is a few hundred pixels across and gets stretched
  over several thousand pixels of arc. Upscaling first is not cosmetic; without
  it the strips are noise.

### One approach tried and abandoned

Detecting where the lettering sits, rather than fixing the bands, sounds
strictly better and was not. Measuring how much the pixels vary around each
radius reliably found the busiest thing in the image: on one seal the central
coat of arms, on another the ring of fine dots edging the border. Restricting
the search outward and blurring away the dots improved it and still recovered
fewer words than fixed bands. Three fixed bands cost eight more recogniser calls
per seal and work on every seal in the corpus. That is the better trade, and the
reasoning is recorded in the code so it is not re-litigated.

## 2. The checking is untouched

`description-check.ts` and `decide.ts` are **byte-identical** — confirmed against
version control, not asserted. Matching is still exact, with no tolerance for
recognition noise.

Last session's four mutations, re-run unchanged:

| Mutation | Tests failed, then | Tests failed, now |
|---|---|---|
| always accept | 7 | **7** |
| stop extracting claims to check | 6 | **6** |
| substring instead of whole-token matching | 1 | **1** |
| drop the length cap | 1 | **1** |

A better reader is allowed to confirm more claims. It is not allowed to lower the
bar for confirming one.

## 3. The canonical document

**Before:** zero of nine expected words. **After:** nine of eleven, including the
entity's name and its charter year. The two still missing are on the inner lower
arc.

The same description that was refused last session is now **published**: a
62-character sentence, well under the 125-character cap, naming the entity
correctly and stating only facts that are words printed on the seal. Every claim
in it was confirmed against the recognised text — the check named them.

Nothing about the central device, the ornaments or the motto appears in it,
because those could not be confirmed and the rule does not allow them.

The remediated document's structure tree now carries a **Figure with a
description** where it previously had an **artifact and nothing**. The
description and the seal's contents are in the private corpus; both name the
entity.

## 4. The refusal still works

Last session's wrong description — wrong name, two wrong dates, wrong motto —
run against the **newly readable** seal:

```
OUTCOME: DECORATIVE (refused)
note: examined; a description was proposed and refused
      (the image does not visibly contain: <four claims, named>)
```

Every wrong value is one character from a correct one, and the seal now plainly
reads the correct ones. All four are still refused. That is the point of exact
matching, and it is why no tolerance was added to absorb recognition noise: the
tolerance that would absorb the noise is the tolerance that would accept these.

## 5. Round seals in the corpus

Page 1 of all 30 corpus documents:

| | |
|---|---|
| Documents scanned | 30 |
| Carrying a round seal or emblem | **13** (43%) |
| Devices found | 13 |
| **Description confirmable** | **13** |
| Falls back to decorative | **0** |

No fallbacks remain. Before the third radial band was added, four of the
thirteen recovered no legible words at all — all four the same device from one
entity, whose lettering sits in a thinner ring further out than the canonical
seal's. Adding a band placed for that geometry fixed all four.

**Two limits on this count, stated rather than buried.** It scans page 1 only:
letterhead seals live there, and reading every page of a 1,180-image budget book
would cost hours to answer a question about letterhead. And "confirmable" here
means the unwrapped reading yields at least two word-shaped tokens — enough to
build and confirm a short description. No judgment model was run to propose a
description for each of the thirteen, so this measures what the reading
supports, not thirteen finished descriptions.

## 6. What it costs

Unwrapping is local processor time. **No model or API cost is added** — the
judgment layer's token usage is unchanged.

| | |
|---|---|
| Recogniser calls per seal | **25** |
| Added time per seal | **4.9s** |
| Documents affected | 13 of 30 |
| Added time averaged over the whole corpus | **+2.1s per document** |
| A one-page document with a seal | **2.6s → 7.5s (2.9×)** |

That last row is the one that matters for pricing. The existing cost table has a
median of 2.6s for a one-page document, and a seal nearly triples it. Averaged
over a mixed corpus the effect is milder — most documents have no round device,
and on a long document 4.9s is noise against the page count — but **the cheapest
documents are the ones most affected in relative terms**, and those are exactly
the single-page agendas the pricing was built around.

Prices were set on the old figures. This does not change what a document costs
to serve in money, only in seconds, but the cheap end of the curve moved and
someone should decide whether it matters before launch.

## 7. The corpus check

| | measured | required | |
|---|---|---|---|
| Score before | **42** | 42 | OK |
| Score after | **100** | 100 | OK |
| Pixels changed | **0%** | 0% | OK |
| Characters extracted | **1,810** | 1,810 | OK |

Structure elements went from 123 to 124: the added element is the `Figure` that
now carries the description.

## Tests

**494 pass** (486 before, 8 added), lint and types clean. The new tests cover the
detector's negative cases — a wide banner, a filled square, an image too small to
carry legible text — and the unwrap itself.

One of them is a size assertion that exists because of the bug below, and it is
the only kind of test that would have caught it.

## Two bugs found on the way, both silent

**The image library's `.metadata()` describes the input, not the pipeline
output.** Reading dimensions from it after a resize returns the *original* size,
so every strip was built from the unscaled image and came out a sixth of the
intended width. Nothing errored. The seal was detected, seventeen recogniser
calls ran, and zero words came back — every signal said the feature was working
except the one that mattered.

**The engine could never have published a description at all.** The attribute
holding it was missing from the class's slot list, so assigning it raised. It was
dead code that nothing had ever executed, because no plan had ever carried a
description — the image pass did not run, so every image took the decorative
branch. The first confirmed description in the project's history found it
immediately.

Both are worth recording for the same reason: the feature *looked* finished from
every angle except a measurement of the thing itself.

## Not in this report

No entity name, nothing of any seal's contents, no document contents. The
before-and-after, the seal image and the survey data are in the private corpus
under `image-rule/2026-09-21/`.
