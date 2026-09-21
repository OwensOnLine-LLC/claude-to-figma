# 0005. Grid band Card count is flexible, not fixed at 6

Status: Accepted
Date: 2026-09-10

## Context

The Grid — Offset with Benefit List template ships with exactly 6 Cards (3
rows of 2), matching the current homepage design. But not every real use of
this template needs 6 — the person building a page may only have 3 or 4
genuine benefits to list, or occasionally more than 6. Treating 6 as fixed
meant either padding out unwanted placeholder Cards or manually
deleting/rebuilding rows by hand every time the count didn't match.

## Decision

`/create-band` asks how many Cards a Grid band needs (default 6, but any
count is accepted) and adjusts the duplicated frame to match, while keeping
the template's 2-column layout: `rows = ceil(N / 2)`, with a trailing single
Card on an odd count. This works because the Grid template's Card layout is
fully auto-layout (container → rows → Cards, `HUG`/`FILL` at every level
except Card width, see `decisions/0004`) — rows can be cloned or removed, and
a lone Card in the last row can be removed from its pair, without manually
touching any spacing, sizing, or position.

## Alternatives considered

**Always build all 6 and let the user delete unwanted ones by hand.**
Rejected — pushes manual Figma work back onto the user for something the
auto-layout structure can already do correctly and quickly.

**A true Figma component/variant system for card count.** Deferred, not
rejected — same reasoning as the band-template-variant deferral in
`decisions/0002`: worth doing once this pattern is more settled, not before.
The clone/remove algorithm is a process convention, not a tool-enforced
constraint — nothing prevents a future edit from breaking the auto-layout
assumptions this relies on.

## Consequences

- Any Card count is fast to produce — no manual row surgery.
- The algorithm depends on the Cards container, rows, and Card frames staying
  auto-layout (`HUG`/`FILL`) as currently built; if that structure changes,
  the clone/remove logic in the skill needs to be re-verified against it
  (this already happened once, see `decisions/0004`'s Card-width fix).
- Icons on any newly cloned Card are standardized to a single placeholder
  icon (`decisions/0003`), not copied from whichever row was duplicated — so
  a higher card count never looks like it's borrowing "real" icon meaning
  from an arbitrary source row.

## Related

- `decisions/0004-band-background-and-card-color.md` — the Card width fix
  this relies on for odd counts to render correctly.
- `decisions/0002-band-template-style-lock.md` — the template-lock rule and
  its precedent for deferring true component/variant systems.
- Skill: `.claude/skills/create-band/SKILL.md` — implements the clone/remove
  algorithm (Step 3, "Adjusting Card count").
