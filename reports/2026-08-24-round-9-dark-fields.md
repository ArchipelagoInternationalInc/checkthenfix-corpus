# Round 9: alternating light and dark fields

**Date:** 2026-08-24 · **NOT DEPLOYED.** On `design-pass`, awaiting owner review
per the brief. Production still serves round 8.

## Zero words moved, proven

The rendered visible text of the rebuilt page and of production is
**byte-identical: 3,207 characters both**. Diffed as rendered text rather than
as source, because a source diff cannot tell a moved word from a moved div.
Every change this round is field, ground and treatment.

Guard counts unchanged: claims 0, house-style 0 across 28 rules, absolutes 0,
dated-claims 0. 411 tests, lint and typecheck clean.

## The two dark fields

The upload panel becomes the page's darkest object. The Fix becomes a full-bleed
band on a navy-to-deep-teal gradient — full-bleed rather than a plate, because a
dark rectangle inset in a white page reads as one more card, and the point of
that section is that it is not another card.

**The gradient stops short of the palette's mid teal on purpose.** Continuing to
`--accent` would have taken white to 5.36 and the bright teal to 2.47 at the
band's lightest point: the gradient would have walked out of AA at one end.

## Every pair measured

A contrast module composites alpha over the real ground — 80% white over navy is
not white — and an audit walks every text node inside both dark fields.

**22 text nodes, zero failures.**

| Pair | Worst case | |
|---|---|---|
| White on the dark fields | **12.18** | AAA |
| White 80% body text | **8.39** | AAA |
| accentBright | **5.62** | AA |
| Sample card text on its white ground | **8.03–17.8** | AA/AAA |

## Three defects the measurements caught

None of these would have survived a careful look, and none of them looked wrong
enough to catch by eye.

1. **The white result card is 72% translucent.** Invisible as a property on a
   light page, and deliberate there. Over navy it turned into a murky blue-grey
   and dropped its own text below AA. It now has a real white ground inside the
   dark panel, which is what makes it the one bright object on the field.

2. **My own first rule repainted text inside that white card.** I wrote a
   descendant selector for the dark panel's muted text; the white card lives in
   the same panel, so its text became white-on-white and vanished. Caught by
   reading the card's computed colour back as `rgba(255,255,255,0.8)` against
   `#ffffff`. Now scoped to the dropzone's own subtree, so the card is untouched
   by construction rather than by luck.

3. **The status line silently lost its colour.** It resolved to the mid teal
   rather than the bright one, because the shared label class is defined later
   in the stylesheet and won on order. It rendered at **2.36:1** — a failure
   whose only symptom was looking slightly dim.

The third is the one worth remembering: a contrast failure does not announce
itself. It looks like a design choice.

## A palette rule narrowed to its own rationale

The bright teal carried a standing rule: **never as text**. Its stated basis was
that it measures 2.17:1 **on white**. Those are not the same statement, and this
round put text on grounds where the same colour measures **5.83** and **5.62**.

The rule now bans what it always meant to ban — this colour as text on a *light*
ground — and the exception is gated on a **computed measurement**, so lightening
either dark field closes it automatically. A companion test asserts the colour
still fails on white, which is the entire reason the rule exists.

This is a narrowing, not a weakening: the ban still fires everywhere it used to
except the two grounds where it was measured safe, under an owner ruling that
authorised exactly that, conditionally.

## The other three rows

Differentiated by treatment rather than decoration, as instructed:

- **Proof strip:** wash plate and glass card both removed. A borderless strip of
  icons, labels and text, so four card rows stop reading as one repeated shape.
- **How-it-works:** white cards and numbers kept, dotted connectors added —
  drawn only *between* cards, and only above the width where the row is
  horizontal. A connector off the last card would point at nothing, and a
  horizontal connector between stacked cards is a line to nowhere.
- **New to this:** unchanged.

Result: five sections, four treatments, one dark anchor plus one dark action.

## Closing CTA — three variants staged

At the review route. Same words in all three; only the field and the treatment
of the free word differ.

| Variant | Measured | |
|---|---|---|
| A — current light | — | unchanged |
| B1 — dark field, bright teal | **5.83:1** | passes AA |
| B2 — dark field, palette red | **2.33:1** | **fails AA** |

B2 is shown because the brief asked for the comparison, and labelled on the page
as unshippable. The red reads hot on white and goes muddy on navy: it is a
mid-tone, and the dark ground removes the lightness difference the colour was
relying on. If the dark CTA is chosen, B1 is the only one of the two that can
carry the word.

## Reduced motion

The settled state is intact behind the new fields: **0 running animations** with
reduced motion, 101 with motion allowed, and both dark fields painted in either
case. The fields are paint, not motion, so they do not participate in the
settled state at all — verified rather than assumed.

## Carried

The 360px H1 five-line wrap is untouched, per the ruling that it is accepted
unless the fix is free. It is not free: it would mean shrinking the 390px
reference.

## Awaiting the owner

- Which page treatment stands.
- The CTA variant. The loser gets deleted.
- Deploy is held until both are decided.
