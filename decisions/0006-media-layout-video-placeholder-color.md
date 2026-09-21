# 0006. Media Layout's video placeholder color follows Band Background too

Status: Accepted
Date: 2026-09-10

## Context

`decisions/0004` established that Card fill must follow the band's
background choice (White band → `#F1F5F9` Cards, Slate Gray band → White
Cards), so placeholder content always keeps contrast against whichever
background is picked. That decision was scoped to Cards specifically.

The Media Layout template has its own placeholder element with the same
problem: the `Video` frame (filled gray — `#F3F4F6`, bound to the file's
`gray/100` variable — since no poster image exists yet at creation time, per
`decisions/0003`). Building a Media Layout band with a Slate Gray background
left the gray video placeholder sitting on a near-identical gray band
background — the same low-contrast failure `decisions/0004` fixed for Cards,
just on a different element, and not covered by that ADR's Card-only wording.

## Decision

The same contrast principle now applies to the `Video` placeholder frame:

- Band Background **White** → `Video` frame stays **`gray/100`** (`#F3F4F6`),
  bound to the existing `VariableID:94:60255` variable — unchanged from the
  template's shipped default.
- Band Background **Slate Gray** → `Video` frame becomes **White**, bound to
  the existing `white` variable (`VariableID:94:60275`) — same binding
  approach used for Cards and for the band's own White background option.

Unlike the Card fill (`#F1F5F9`, a literal with no matching token —
`decisions/0004`), the Video frame's default gray is *already* bound to a
real variable (`gray/100`). That binding is preserved when the band is White;
only the Slate Gray case swaps it to the `white` variable.

## Alternatives considered

**Extend `decisions/0004` in place to cover this case.** Rejected — ADRs are
not edited once Accepted; the reasoning here (an already-tokenized default,
a different element, a different template) is distinct enough to warrant its
own record rather than silently broadening an existing one.

**Leave the video placeholder gray regardless of band background.** Rejected
— this is exactly the low-contrast problem already fixed for Cards; leaving
Media Layout unfixed would be an inconsistent, arbitrary exception with no
design rationale behind it.

## Consequences

- Media Layout and Grid bands now follow the same contrast rule for their
  respective placeholder elements (Video frame vs. Cards), even though the
  underlying fill values and binding states differ between them.
- Any future card- or placeholder-bearing template should be checked for the
  same low-contrast risk against a Slate Gray band background before
  shipping it through `/create-band`.

## Related

- `decisions/0004-band-background-and-card-color.md` — the precedent this
  extends, and the origin of the White-binding approach reused here.
- `decisions/0003-placeholder-first-band-creation.md` — why the Video frame
  is still showing a placeholder fill at all (poster/video are deferred).
- Skill: `.claude/skills/create-band/SKILL.md` — implements this rule
  alongside the Card color-coupling logic in Step 3.
