# 0004. Band background is White or Slate Gray; Card fill follows it automatically

Status: Accepted
Date: 2026-09-10

## Context

Every band frame has its own background fill (White by default, matching the
file's `white` color variable). A second background option — Slate Gray,
`#F4F7FC` — was added so bands can alternate against a page's white sections.
Grid-layout bands also have Cards with their own fill (`#F1F5F9` by default),
sitting on top of the band background. Once Slate Gray became an option, the
existing card fill (`#F1F5F9`) — chosen for contrast against a White band —
read as nearly invisible against a Slate Gray band background, since the two
colors are close in lightness.

Separately, in the course of testing this, the Card frames' width behavior was
found to be `layoutSizingHorizontal: FILL` with `layoutGrow: 1`: fine for a
full row of 2, but it made a lone Card in a trailing odd-count row stretch to
the full row width instead of staying at normal column width. This was fixed
directly on the master template (structural revision, not a content-flow
change) to `FIXED` width (~468.66px, `layoutGrow: 0`).

## Decision

Card fill is derived automatically from the band's background choice — never
asked separately:

- Band background **White** → Cards stay `#F1F5F9` (the template's shipped
  default).
- Band background **Slate Gray** → Cards become **White**, bound to the
  file's `white` variable.

This keeps Cards visually distinct from the band background in both cases,
without adding a third question to Step 2. The rule is written generally (any
band with Cards, not just the current Grid template) so it extends
automatically if a future card-bearing template is added.

Card width was separately fixed to `FIXED` (~468.66px) on the master
template, so a lone trailing Card in an odd-count row keeps its normal column
width instead of stretching to fill the row — this is what makes odd card
counts (see `decisions/0005`) look correct rather than lopsided.

## Alternatives considered

**Ask for Card color as its own Step 2 question.** Rejected — the whole point
of the two real background colors is that they're a deliberate, limited
palette; letting Card color vary independently multiplies the combinations
without any real design intent behind most of them.

**Bind `#F1F5F9` to a new or existing color variable.** Rejected for now — no
existing token matches `#F1F5F9` or `#F4F7FC` exactly, and approximating with
a close-but-different token (e.g. `gray/100`) would silently shift the actual
color. Left as literal values; promoting either to a real token is a separate
design-system decision, not a side effect of this flow.

## Consequences

- Background and Card color always pair correctly — no combination exists
  where Cards blend into the band.
- The two literal colors (`#F1F5F9`, `#F4F7FC`) aren't backed by design
  tokens yet; if the design system later adds matching tokens, this flow
  should be updated to bind to them instead of using literals.
- The Card width fix and the color-coupling rule are independent but easy to
  conflate — width behavior is about layout (stretching), color coupling is
  about contrast. Both are needed for a trailing solo Card to look right.

## Related

- `decisions/0005-card-count-is-flexible.md` — odd counts are exactly where
  the Card width fix matters visually.
- Skill: `.claude/skills/create-band/SKILL.md` — implements both rules
  (Step 3's background/Card-color section and the Card-count structural
  notes).
