# 0007. Every band gets Dev Mode Tailwind class annotations on its colored nodes

Status: Accepted (Card-fill row superseded by `0016` — annotate once per
repeated set, not once per Card; Band background and `Video` frame rows
unaffected, both remain single-instance nodes)
Date: 2026-09-10

## Context

`/create-band` already derives every color-bearing node's fill mechanically from
the Band Background choice — the band's own frame (`decisions/0004`), Card fill
(`decisions/0004`), and the Media Layout `Video` placeholder (`decisions/0006`).
Two of those literal or bound colors turn out to already be exact Tailwind
default-palette stops: `#F1F5F9` (Card fill on a White band) is `slate-100`,
already recorded in `decisions/0001`'s site-wide Tailwind alignment table, and
`#F3F4F6` (`gray/100`, the Video frame's default) is Tailwind's own `gray-100` —
the Figma variable's name was presumably chosen to track this. `#FFFFFF`
(`white`) is Tailwind's `white` keyword outright. Only `#F4F7FC` (the Slate Gray
band background itself) has no matching default stop, consistent with
`decisions/0004`'s finding that it isn't backed by any design-system token
either.

Dev handoff for a freshly generated band currently requires a developer to
manually work out, hex by hex, which of these apply — repeating a lookup this
flow already has all the information to resolve automatically, since it just
set every one of those fills.

## Decision

Every band `/create-band` produces gets a Dev Mode annotation on each node it
sets a fill on (band background frame, `Video` frame, every Card), stating the
Tailwind class a developer should use, resolved from the fill the flow already
applied:

| Node | Resolved color | Tailwind class |
|---|---|---|
| Band background — White | `#FFFFFF` | `bg-white` |
| Band background — Slate Gray | `#F4F7FC` | `bg-[#F4F7FC]` (arbitrary value — no default stop matches) |
| `Video` frame — White band | `#F3F4F6` / `gray/100` | `bg-gray-100` |
| `Video` frame — Slate Gray band | `#FFFFFF` / `white` | `bg-white` |
| Card fill — White band | `#F1F5F9` | `bg-slate-100` |
| Card fill — Slate Gray band | `#FFFFFF` / `white` | `bg-white` |

This applies to every band produced going forward, not only the two current
templates — any future card- or placeholder-bearing template inherits the same
rule automatically, same scoping as `decisions/0006`.

Annotations are appended to a node's existing `annotations` array, never
assigned as a flat overwrite — the `Video` frame already picks up a second,
unrelated annotation later (the video source, once a real asset lands per the
existing Step 3 point-4 workflow), and an overwrite there would silently delete
whichever annotation was written first.

## Alternatives considered

**Compute the Tailwind class at audit/dev-handoff time instead of at creation
time.** Rejected — the mapping only needs the fill that was just set, which
this flow already knows precisely; deferring it to a later audit pass means
re-deriving the same lookup from a screenshot instead of from the actual
applied value, with more room for error.

**Bind `#F4F7FC` to a new Tailwind-matching token instead of using an
arbitrary-value class.** Rejected — this is a design-token promotion, the same
call already declined in `decisions/0004` for a different reason. Annotating a
class doesn't require the underlying Figma fill to be tokenized; `bg-[#F4F7FC]`
is a legitimate Tailwind utility on its own.

## Consequences

- A developer implementing any freshly generated band can read the exact
  Tailwind class off the frame in Dev Mode instead of re-deriving it from a hex
  value.
- If `decisions/0001`'s site-wide mapping table is ever revised (e.g. a future
  rebrand moves `slate-100` to a different family), this table must be
  re-checked for drift — it currently piggybacks on that table's
  `Fog → slate-100` entry for the White-band Card case.
- `#F4F7FC` remains a design-system gap: it now has a documented Tailwind
  class, but not a proper token. That gap is `decisions/0004`'s, not
  introduced or resolved here.
- This is forward-looking only — bands already produced before this decision
  (if any) do not get retroactively annotated as a side effect of it.

## Related

- `decisions/0001-tailwind-alignment-homepage-colors.md` — the site-wide
  hex-to-Tailwind mapping this table draws from (`#F1F5F9` → `slate-100`).
- `decisions/0004-band-background-and-card-color.md` — the Band Background /
  Card fill coupling this annotates.
- `decisions/0006-media-layout-video-placeholder-color.md` — the Video frame
  coupling this annotates.
- Skill: `.claude/skills/create-band/SKILL.md` — implements the annotation
  step in Step 3.
