# 0016. Repeated Dev Mode annotations collapse to one instance, not one per repetition

Status: Accepted
Date: 2026-09-14

## Context

Building a live demo band to evaluate a proposed content-schema annotation
(a separate, still-open piece of work) surfaced a problem with an existing,
already-shipped rule: `decisions/0007` says every band gets a Dev Mode
annotation "on each node it sets a fill on... every Card." Taken literally,
a 6-Card band gets six identical `**Tailwind:** \`bg-slate-100\`` annotations,
one per Card, none of them saying anything the other five don't already say.

Seeing this rendered for the first time (rather than reasoning about it in
the abstract when `0007` was written) made the redundancy obvious in a way
the original design review missed: user feedback was direct — "too much...
it is obvious enough that the background for each card is bg-slate-100,
just keep 1 and dont repeat duplicate annotations." This is the same class
of lesson `decisions/0012` already learned for sizing bugs (a property looks
fine reasoned about in isolation, the actual rendered result is what catches
it) — here applied to Dev Mode annotations instead of layout.

The band background frame and the Media Layout `Video` frame are each a
single node per band, so `0007`'s rule never over-counted for those — this
problem is specific to Cards, the one node type that repeats N times within
one band (`decisions/0005`: Card count is flexible, not fixed).

## Decision

**For any set of structurally repeated sibling nodes that share the same
resolved Tailwind class, annotate only the first instance in reading order**
(first row, first column), stating that the annotation applies to the whole
set: `` **Tailwind:** `bg-slate-100` (applies to all N Cards in this grid) ``,
where `N` is the actual Card count for that band instance (per `0005`, not a
hardcoded number). Every other instance in the set gets no annotation at all.

This generalizes beyond Cards specifically: any future template that
introduces another repeating node type (a chip list, a stat row) follows the
same rule without needing a new ADR each time the pattern recurs.

This narrows `decisions/0007`'s Card-fill row only. The band background
frame and `Video` frame rows are untouched — they were never over-annotated,
since there's only ever one of each per band.

## Alternatives considered

**Move the annotation to the Cards container instead of any individual
Card.** Rejected — the container has no fill of its own (`fills: []`, a
deliberate rule so the band's own background shows through an empty grid
cell, per the skill's existing "no fill on the Cards container" note) and
isn't where a developer inspecting a specific Card in Dev Mode would think
to look. Keeping the one annotation on the first real Card stays discoverable
while still cutting five of six duplicates.

**Drop the Card-fill annotation entirely, relying on `decisions/0001`'s
site-wide hex-to-Tailwind table.** Rejected — that requires a developer to
already know a Card's fill maps to the `Fog → slate-100` entry in a different
document; the whole point of `0007` was resolving that lookup automatically
at the point of use. One annotation preserves that benefit at zero
redundancy cost.

**Keep one annotation per row (2 per band instead of 6), rather than one per
band.** Rejected — every Card in a row shares the same fill for the same
reason every Card in the grid does (Card color follows Band Background,
`0004`); there's no visual or structural distinction between rows that would
make a second annotation informative.

## Consequences

- `.claude/skills/create-band/SKILL.md`'s Step 3 needs an edit: the fill
  logic still runs on every Card (unchanged — Card color still follows Band
  Background per `0004`), but the annotation call runs only on the first
  Card, worded with the actual count.
- The demo band built to evaluate the content-schema annotation (`2947:67`
  on Band Drafts) had this exact over-annotation and needs its five
  duplicate Card annotations removed, its remaining one reworded to match
  this rule — done as part of landing this ADR, not left as stale evidence.
- Any band built between `0007` landing and this ADR (if any exist beyond
  the one demo band) carries the same six-way duplication and should be
  corrected the next time it's touched, not retroactively swept as a side
  effect of this decision.
- The content-schema annotation work this was discovered while evaluating
  remains open and separate — this ADR only fixes the pre-existing `0007`
  behavior it happened to expose.

## Related

- `decisions/0007-tailwind-class-annotations-on-band-nodes.md` — the rule
  this ADR narrows (Card-fill row only).
- `decisions/0004-band-background-and-card-color.md` — why every Card shares
  one fill in the first place, which is what makes one shared annotation
  correct instead of a loss of information.
- `decisions/0005-card-count-is-flexible.md` — why the annotation's count
  must be read from the actual Card count, never hardcoded.
- `decisions/0012-responsive-text-container-audit.md` — the same "verify the
  actual rendered/displayed result, not the rule in the abstract" discipline,
  applied here to annotations instead of layout.
- Figma: demo band `2947:67` on Band Drafts (`2686:15`) — where this was
  found and where the fix lands.
