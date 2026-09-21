# 0011. Author content mobile-first, but ship two purpose-built variants, not one cascaded either direction

Status: Accepted
Date: 2026-09-10

## Context

`decisions/0010` established what may never be cut from mobile and what's a
legitimate condense/collapse candidate. It didn't answer a prior question:
in which direction should content actually be *authored* — start from desktop
and trim down, start from mobile and stretch up, or something else? Three
concrete options were on the table:

1. Optimize content for mobile, cascade that same content up to desktop
   unchanged (just reflowed/enlarged).
2. Expand content for desktop, author a separately concise-but-complete
   version for mobile, keeping the substance in parity per `decisions/0010`.
3. Optimize content for mobile, cascade it up to desktop, and don't apply
   content parity at all — i.e. let desktop end up as thin as mobile.

Owens OnLine's desktop pages carry the site's E-E-A-T and trust substance —
compliance framework detail, in-country investigator language, quantified
proof — precisely because desktop has the room for it. Any option that caps
desktop at mobile-level brevity throws that away for no UX benefit, since
desktop users aren't scroll-constrained the way a decision like that would be
trying to solve for.

## Decision

**Option 2, refined:** use a mobile-first *authoring process*, but the
*output* is always two deliberately different, purpose-built variants — not
one variant cascaded in either direction.

- **Process:** when writing or condensing a section, start from the tightest,
  most essential version of the message — what does this section absolutely
  need to say — before deciding what desktop adds on top. This is the
  discipline of mobile-first design (identify the essential content before
  the luxury of extra space invites padding), applied to copywriting, not
  just layout.
- **Output:** desktop gets the fuller, more elaborated version where space
  allows; mobile gets a genuinely rewritten concise version — not the same
  copy reflowed, not desktop copy mechanically shortened by truncation — as
  long as `decisions/0010`'s never-cut list survives and Criterion E's
  entity-attribute-context test passes.

Option 1 (mobile content cascaded up, desktop never expanded) is rejected as
the final output, though its process discipline is exactly what "mobile-first
authoring" above keeps. Option 3 (mobile content cascaded up, no parity
applied) is rejected outright — it isn't a real content-parity strategy, it's
starving the version of the page that has no space constraint forcing it to.

This creates no conflict with the root `CLAUDE.md` priority order (Conversion
Path → Trust → Clarity → Friction → Accessibility → SEO/GEO): option 2 wins on
clarity and trust before the SEO/GEO benefit is even counted, so there's
nothing to arbitrate between this ADR and that ordering.

## Alternatives considered

**Desktop-first authoring: write the full desktop version, then trim for
mobile.** Rejected as the default process — starting from a sprawling draft
and hoping trimming preserves the signal is less reliable than starting from
the essential version and deciding what to add. (`decisions/0010`'s own
Criterion E language — "condense up to the signal word, not through it" —
describes exactly the failure mode this avoids by not starting there at all.)

**Option 1 as both process and output — mobile content literally cascaded to
desktop.** Rejected — under-uses desktop's screen real estate and cuts against
the Master Criterion's requirement that human-verification trust signals be
*dominant*, not merely present; desktop has the room to make that case more
fully and should.

**Option 3 — no parity at all.** Rejected outright; see Context. This is the
one option with a real, identified downside and no offsetting benefit.

## Consequences

- Future copywriting passes (like the condensing already applied to the
  base/mobile mockup's Hero, narrow band, and wide band paragraphs) should
  start by identifying the mobile-essential version of a claim, then decide
  what desktop's version adds — not the reverse.
- Desktop copy is not required to match mobile's brevity, and mobile copy is
  not required to match desktop's elaboration — divergence between the two is
  the expected, correct outcome of this ADR, not something a future audit
  should flag as inconsistency.
- `decisions/0010` still governs *what* must never be cut; this ADR governs
  the *direction* content is authored in. Both apply together to any future
  condensing pass.

## Related

- `decisions/0010-mobile-content-parity-policy.md` — what must survive
  condensing; this ADR governs how the condensed version gets written.
- `CLAUDE-MOBILE.md` — Criterion E (UX-to-SEO weighting, "condense up to the
  signal word") is the specific mechanic this ADR's authoring process is
  built around.
- Figma: base/mobile (375px, node `2834:47722`) frame on Marketing Pages
  R&D — where this direction was first applied, condensing 9 paragraph
  blocks across Hero, the narrow band, and the wide band.
