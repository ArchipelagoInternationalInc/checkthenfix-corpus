# Hero per the owner's comp, navigation, mobile pass, register

**Date:** 2026-08-25 · **Deployed 18:31:02Z.** Six-line verification TRIPLE
GREEN. Review route 200.

## Slice A — hero rebuilt to the comp

The comp replaced the A/B pair outright, so the variant switch went with them.

**Anatomy delivered:** 60/40 columns. Left — eyebrow, H1, subhead with red FREE,
paragraph, hairline rule, privacy line small and italic beneath. Right — the
dark panel compacted to the upload action alone: rounded, inner dashed border,
icon chip upper right, condensed-caps white headings in two sizes, one
instruction line, violet glow at the base. Beneath it the sample result card as
a free-standing glass card, contents unchanged.

**Acceptance:** H1 at **16.3%** of a 1080 viewport. Panel **180px**, above the
fold with **+703px** at 1920×1080 and **+523px** at 1440×900. Nothing overflows
at 390px — or at 430, 360 and 320.

### Two things done deliberately differently from the comp

**The caps are CSS, not copy.** `text-transform` renders the approved
sentence-case strings as the comp's condensed caps. The source strings are
unchanged, so a copy guard still matches them and assistive technology is not
handed shouted text. Terminal stops are gone because these are condensed-face
labels now, and the standing punctuation rule takes stops off labels site-wide.

**The comp's privacy wording is not shipped.** It is not a rewording of the
approved line — it is a broader claim. "All submitted documents are private and
encrypted" covers authenticated uploads; the approved sentence scopes deletion
to anonymous ones. It is registered as a proposed variant and added to the
held-claims register for counsel, so the owner's draft is preserved without
being mistaken for approval of its words.

The instruction line is the owner's own, with the brief's typo correction, and
the size still renders from configuration rather than the literal "50" — a cap
change must not turn that sentence into a false statement.

## The header did not fit a phone, and my own check missed it

The bar wanted about **417px inside a 350px shell** at 390px.

The overflow check that should have caught it compared against
`window.innerWidth` — which **under mobile emulation reports the visual
viewport (509px), not the layout viewport (390px)**. A real overflow measured as
none. Both measurement scripts now use `documentElement.clientWidth`.

The wordmark was the actual consumer at **207px** of that 350: shaving the
button alone could never have closed a 44px gap. Below 560px the button sheds
padding and the menu becomes its glyph with its accessible name intact; below
380px the shell's gutters give as well. **Verified clean at 430, 390, 360 and
320.**

Two more caught in the same pass: the header CTA rendered **underlined**
(`.btn-primary` was written for `<button>`, which carries no link decoration),
and the panel's subtitle announced **"Free check ,no account needed"** to a
screen reader — the bullet is decorative, the comma stands in for it, and the
space had been left on the visual side.

## A type guard fired, and it was right

The new headings declared `font-family` directly. The type system is a fixed set
of selectors, and a fourth place naming a face is how that system stops being
one. They use the existing condensed-label class now, with white overriding its
accent colour on the dark ground.

## Slices B, C, D — re-verified against the rebuilt hero

| | |
|---|---|
| Contrast, 1440 and 390 | **35 nodes, 0 failures** at both |
| Upload panel worst margin | **+5.57** |
| Bloom floors | **ALL MET** at both widths |
| Reduced motion | **0 running animations**, blooms painted, footer dark |
| Nav | live; Privacy and Terms **absent, not dead** |

**The upload panel had dropped out of the contrast measurement entirely** when
its class changed with the rebuild — the script was still looking for the old
selector and silently measured nothing there. Retargeted, then measured. A
scan that quietly covers less than it did is worse than one that fails.

### Register status

| Item | Status |
|---|---|
| Image handling | **CLEARED.** Drafts flagged only when undescribable, hedged, or below confidence; otherwise written in unflagged. The homepage sentence stays out |
| Reading-order transcript | **NOT CLEARED.** Nothing produces one, in any report type |
| Validator on free checks | **CLEARED.** Asserted specifically for the free check, with a negative half |
| Score formula + rounding | **CLEARED.** Truncation, not rounding: 9/21 reports 42 |
| Scope detection before payment | **HALF.** Detection is tested; `lib/payments/` is one empty `.gitkeep`, so there is no charge for it to run before |

## Delegation log

| Call | Decision | Basis |
|---|---|---|
| Deploy | Merged to `main`, deployed | Standing delegation; brief asks for gate-review and preview |
| Variant switch | Deleted with the old variants | The comp replaces slice A entirely |
| Caps | Rendered via CSS, not retyped | Copy is locked; the caps are a rendering |
| Privacy wording | Approved line shipped; comp's registered | Comp's line is a broader claim, and every clause is already on the register |
| Header at 320px | Fixed rather than reported as a limit | The fix was gutters and artwork scale, not a redesign |

421 tests, lint and typecheck clean. Copy locked; lattice untouched.
