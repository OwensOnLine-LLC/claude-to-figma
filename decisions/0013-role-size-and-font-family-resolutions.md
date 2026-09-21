# 0013. Eyebrow and card-description sizes unified; font family set to Inter; two other open items ratified as-is

Status: Accepted
Date: 2026-09-11

## Context

`decisions/0008` found five genuine design decisions that Tailwind's scale
doesn't arbitrate — two candidate values were both valid tokens, so the ADR
left them explicitly open rather than picking a winner disguised as a
Tailwind-compliance fix:

1. Eyebrow role size: `text-sm` (Hero) vs. `text-lg` (Section)
2. Card-description role size: `text-base` (feature cards) vs. `text-lg`
   (product cards)
3. Page-heading role size: `text-5xl` (homepage) vs. `text-6xl` (interior)
4. System font family: Inter/Inter Tight (the two `/create-band` templates)
   vs. Neue Haas Grotesk Text Pro (what the live homepage actually ships)
5. Whether the full-bleed container variant should keep existing as its own
   pattern, or be folded into the standard container

This ADR is the human decision `0008` deferred. It also surfaced a live
inconsistency while checking current state before applying anything: the
narrow-band eyebrow ("PEOPLE, NOT DATABASES") was already 14px on the
base/mobile mockup but 18px on `lg` — the same text, same role, silently
different between two mockups that `decisions/0009` already says should carry
no breakpoint variation for eyebrows. That drift had gone uncaught until this
pass specifically checked actual current values rather than trusting the
`0008` table's original measurements.

## Decision

**1. Eyebrow → unified to `text-sm` (14px), every instance, every
breakpoint.** Hero's eyebrow was already 14px and needed no change. Fixed:
narrow-band eyebrow on `lg` (18→14, closing the drift described above), and
wide-band eyebrow on both mobile and `lg` (18→14 each).

**2. Card description → unified to `text-base` (16px).** No change was
actually required in either mockup we've built — the narrow-band cards and
the wide-band cert cards were already 16px on both mobile and `lg`. The
16-vs-18 conflict `0008` found came from a different part of the real site
(a "product card" component not yet touched by any mockup here). This
decision now governs that component whenever it is built or audited: its
description text should be 16px, not 18px.

**3. Page-heading size → ratified as-is, not unified.** `text-5xl` (homepage
headline) and `text-6xl` (interior H1) are two *intentionally different*
roles, not the same role shipped inconsistently: the homepage headline
repeats across many bands sharing a page with hero imagery, while an interior
page has exactly one H1 with no competing visual noise, and giving it more
weight is a normal, defensible pattern (`decisions/0009` already modeled them
as separate roles without explicitly stating this reasoning — this ADR
supplies it). No change to any mockup.

**4. System font family → Inter / Inter Tight.** The two `/create-band`
master templates already spec this; the live homepage is the one that needs
to migrate, not the templates. This is a ratification of *intent*, not an
implementation — migrating the live homepage's actual type from Neue Haas
Grotesk Text Pro to Inter/Inter Tight is a large, separate, site-wide change
and is explicitly **not** done as a side effect of this ADR. It also doesn't
resolve the tooling gap noted throughout this project: Inter Tight isn't
loadable in this Figma environment, so the `{ family: 'Inter', style: 'Bold'
}` stand-in remains necessary for any Figma work touching this role, per the
existing convention.

**5. Full-bleed container variant → ratified as a legitimate second
pattern.** Kept as-is, not folded into the standard `max-w-screen-2xl`
container: it exists specifically to give dense grid sections more room, and
removing it would only reduce a real, currently-used capability with no
offsetting benefit. No change.

## Alternatives considered

**Force eyebrow to `text-lg` instead of `text-sm`.** Considered — `text-lg`
was the more visually prominent option and already Section's value. Rejected
in favor of `text-sm`: an eyebrow is conventionally a small kicker label, and
`text-sm` was already correct on both Hero instances and one of the two
narrow-band mockups, minimizing the actual footprint of the change.

**Treat items 1 and 2 as "keep both, intentional hierarchy" rather than
unifying.** Considered for both — a real option on the table. Rejected in
favor of unifying: unlike item 3 (page heading), no reasoning was ever given
for why an eyebrow or a card description should differ by context, and
unifying removes a class of "is this a bug or on purpose" question for anyone
auditing the design later.

**Decide the font-family question by inspecting which one the business
actually prefers, rather than asking directly.** Not attempted — this is a
brand decision with no technical signal to infer it from; guessing would
have been exactly the kind of unrequested design decision this whole ADR
chain has been careful to avoid making unilaterally.

## Consequences

- Three real edits landed: narrow-band eyebrow (`lg`, 18→14px) and wide-band
  eyebrow (mobile and `lg`, 18→14px each). Verified visually on both
  breakpoints — no wrapping or overflow introduced.
- No edits were needed for card-description sizing — both mockups already
  complied with the now-ratified `text-base` rule.
- The font-family decision is ratified but **not implemented**: the live
  homepage's actual migration to Inter/Inter Tight is unscoped, separate
  follow-up work, not triggered by this ADR.
- `decisions/0008`'s "Explicitly NOT resolved" list is now fully closed. Any
  future audit should treat all five of its items as resolved per this ADR,
  not as still-open questions.
- `context/tailwind-tokens.md` needs its 🟡 flags removed for eyebrow and
  card-description size (now single values, not conflicts) and a note added
  for the font-family and page-heading resolutions.

## Related

- `decisions/0008-tailwind-aligned-layout-and-type-rules.md` — the ADR whose
  open items this one resolves.
- `decisions/0009-responsive-type-scale.md` — already modeled homepage
  headline and Interior H1 as separate roles; this ADR is the explicit
  reasoning for treating that as intentional rather than drift.
- `decisions/0012-responsive-text-container-audit.md` — the narrow-band
  eyebrow's mobile-vs-`lg` drift is exactly the class of bug that ADR's audit
  discipline exists to catch; it was caught here by checking current values
  before applying a decision, not by a dedicated audit pass.
- `context/tailwind-tokens.md` — developer-facing reference; update in the
  same spirit as this ADR's own Consequences note above.
- Figma: "Owens OnLine Homepage — lg (1024px)" (`2822:47699`) and "— base/mobile
  (375px)" (`2834:47722`) frames on Marketing Pages R&D, where the three
  eyebrow fixes were applied.
