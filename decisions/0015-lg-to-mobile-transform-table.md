# 0015. A single lg→mobile transform table drives breakpoint generation, not a second set of hand-built masters

Status: Accepted
Date: 2026-09-14

## Context

`/create-band` (`decisions/0002`) produces exactly one frame, at the single
width the Templates page masters (`2683:82644`, `2683:82662`) happen to be
built at. It has no notion of a mobile variant at all. Asked whether the
project has "all the specs" needed for "create bands" to mean "create bands
for every screen size we currently have" (base/mobile 375px and `lg` 1024px,
the two breakpoints already established on Marketing Pages R&D), the honest
answer was no — a full gap audit found:

- No mobile master template exists; only one width per band type has ever
  been built.
- The existing masters are themselves not yet aligned to `0008`/`0009`/`0013`
  — e.g. the skill's cached font-map still lists Eyebrow at 18px, not the
  `text-sm` (14px) `0013` ratified. `0008`'s own Consequences section already
  flagged this: the Tailwind-aligned values were never applied to the
  masters, only reasoned about.
- The grid-reflow behavior confirmed correct during this session's `lg` audit
  (wide band's cert-card grid: genuine 2-column at `lg`, stacked with a
  uniform gap at mobile) has only ever existed as an applied fix on one
  hand-built band, never as a rule anything could reuse.
- Every actual breakpoint delta that does exist (container gutter, vertical
  rhythm, type scale, line-height, tracking) is already fully decided —
  scattered across `0008`/`0009`/`0012` and compiled once already in
  `context/tailwind-tokens.md` — but nothing turns those decisions into
  something a generation step can walk mechanically.

The real fork was: do the masters get manually duplicated per breakpoint (a
second, hand-maintained mobile master per band type), or does mobile get
derived at generation time from one table of already-decided deltas applied
to the single lg master? The scalability question mattered directly — this
project has already paid for the duplicate-source-of-truth mistake once
(three disagreeing container systems, two disagreeing footer paddings found
in `0008`'s raw audit), and any answer that reintroduces a second
hand-maintained artifact per template repeats that failure mode structurally,
at 2x the surface, every time a new template or breakpoint is added later.

## Decision

**Mobile is derived, not authored, for every structural property.** A single
lookup table — the "Breakpoint Transform Table," added to
`context/tailwind-tokens.md` as the code-facing reference this generation
step actually consumes — captures every lg→mobile delta already decided by
`0008`/`0009`/`0012`. `/create-band` (and, going forward, the single-band
subagent) builds the lg instance from the master as today, then derives the
mobile instance by walking this one table. No second physical master frame
is ever hand-built or hand-maintained per template.

This explicitly does **not** cover content. `0011` already settled that copy
is authored per-variant, not cascaded — this ADR only mechanizes the
*structural* half (spacing, type scale, line-height, tracking, grid-reflow
behavior), which is deterministic and was never the part `0011` was
protecting.

Two follow-up actions this decision requires, tracked here so they aren't
lost:

1. **One-time master cleanup.** The lg masters themselves need a template
   revision (per `0002`, done explicitly, not as a side effect) to match
   `0008`/`0009`/`0013` before any instance built from them is correct —
   starting with the eyebrow-size drift found above. This is a prerequisite
   for the transform table to produce correct output, not something the
   table itself fixes.
2. **Grid-reflow generalized as a rule**, not left as a one-off fix: any
   future Grid-type band's card layout follows the same lg-2-column /
   mobile-stacked-uniform-gap pattern the wide band now correctly
   demonstrates, captured as a row in the transform table below rather than
   re-discovered per new band.

## Alternatives considered

**Hand-build and maintain a second mobile master per band type.** Rejected —
doubles the maintenance surface every time a new template is added, and
puts the project back in the exact failure mode `0008`'s raw audit exists
to document (multiple hand-maintained sources for the same value drifting
independently). A table has one row to update; a second master has an
entire frame to keep in sync by hand.

**Let each new band's mobile variant be built ad hoc, reactively, the way
the four existing Marketing R&D bands were.** Rejected as the standing
process — it's what produced the three-separate-discoveries bug history
`0012` documents. Fine as how the *first* four bands got built (there was no
table yet to walk), wrong as the permanent method now that one exists.

**Defer this whole question until the single-band subagent is actually being
built, rather than fixing `/create-band` or writing this table now.**
Rejected — the table is useful immediately regardless of which system ends
up consuming it (today's skill, or the future subagent), and every value in
it is already decided; there's no reason to gate compiling it on Phase 1
starting, the same reasoning `0014` used for its own rules.

## Consequences

- `context/tailwind-tokens.md` gains a new table (see that file) compiling
  every already-decided lg→mobile delta into one generation-ready lookup,
  plus the newly-generalized grid-reflow rule.
- `/create-band`'s `SKILL.md` still needs its own edit to actually call this
  table when generating a new band — writing the table is a prerequisite for
  that edit, not a replacement for it. Tracked as follow-up work, not done in
  this ADR.
- The one-time master cleanup (item 1 above) is still outstanding — the
  masters remain un-migrated until that separate action happens. Any band
  built from them before that cleanup lands still carries the eyebrow-size
  (and possibly other) drift.
- `decisions/0012`'s audit step still runs after generation regardless of
  this table — a correct transform table reduces how often the audit finds
  something, it doesn't replace the audit.
- This table does not decide anything about content condensing, image
  visibility, or copy — those stay `0010`/`0011`'s territory, per-band human
  judgment calls, not mechanical lookups.

## Related

- `decisions/0002-band-template-style-lock.md` — why the master cleanup this
  ADR requires must be its own explicit action, never a side effect.
- `decisions/0008-tailwind-aligned-layout-and-type-rules.md`,
  `decisions/0009-responsive-type-scale.md` — the source of every delta value
  compiled into the transform table.
- `decisions/0010-mobile-content-parity-policy.md`,
  `decisions/0011-content-authoring-direction.md` — why content is
  deliberately excluded from this table's scope.
- `decisions/0012-responsive-text-container-audit.md` — the grid-reflow
  finding this ADR generalizes into a standing rule, and the audit step this
  table's output still has to pass.
- `decisions/0013-role-size-and-font-family-resolutions.md` — the eyebrow/
  card-description resolutions the master cleanup applies.
- `decisions/0014-agent-guardrails-adapted-from-sdlc-governance-blueprint.md`
  — the Phase 0/Phase 1 framing this ADR's timing follows.
- `context/tailwind-tokens.md` — where the actual transform table lives.
- `.claude/skills/create-band/SKILL.md` — the skill this table is a
  prerequisite for updating.
