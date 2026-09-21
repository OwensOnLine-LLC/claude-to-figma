# 0002. Band templates are read-only references; content is the only variable

Status: Proposed
Date: 2026-09-08

## Context

Band sections get regenerated repeatedly: Owens supplies new headline/body copy and
images for a new page or campaign, and Claude is prompted to build the Figma frame
for it. The two original reference layouts live on the Templates page (node-id
`2683-14127`) — "Feature Band - Media Layout - Content with Video" and "Feature
Band - Grid - Offset with Benefit list." Without a locked reference, each
generation is a fresh interpretation of "a feature band," which risks drifting
spacing, type scale, icon treatment, and grid proportions from one instance to the
next — the opposite of what a template is for. There is also a distinct risk that
generating a new instance edits the template frame itself, which would corrupt the
one source future instances are supposed to match.

**Update, 2026-09-14 — a third full template, plus a new tier of smaller reusable
components.** The "true component/instance system" deferred in Alternatives below
was deferred specifically because "band variants beyond these two haven't been
defined" — that's no longer true. A third full band was added
("Content band (narrow) — Tailwind Aligned," built at the corrected 1536px/192px
container from `0008`), and, separately, six smaller reusable pieces were added by
decomposing the existing templates into their recurring structural parts —
prompted by the custom-band work in progress (see `multi-band-page-agent-goal`
memory note): a template-clone model has nothing to offer once a band's structure
isn't one of the fixed, whole shapes already built. This section documents that
expansion; the true-instancing alternative itself remains deferred (confirmed
2026-09-14 — frame-cloning is still the intended mechanism for now, revisit only
if actually needed later).

## Decision

The frames under Templates (node-id `2683-14127`) are read-only masters. They are
referenced, never modified — no new design work is ever performed by editing a
template frame in place. This applies equally to full band templates and to the
smaller reusable components added 2026-09-14 (below) — same rule, same
enforcement, just two different grain sizes of the same "reference, never edit"
principle.

**Full band templates (whole, clonable via `/create-band` Step 1):**

| Name | Node ID | Notes |
|---|---|---|
| Feature Band - Media Layout - Content with Video | `2683:82644` | Original. |
| Feature Band - Grid - Offset with Benefit list | `2683:82662` | Original. |
| Content band (narrow) — Tailwind Aligned | `2951:67984` | Added 2026-09-14. Composed from three of the reusable components below (Headline Section - Top Center, Card Grid - 4 Column Center, Content with CTA - Bottom Center) — kept as its own governed template rather than only existing as their assembly, since it's a real, previously-shipped band shape (the narrow content band on Marketing Pages R&D), not just a demo of composition. |

**Reusable components (atomic, added 2026-09-14 — raw material for future
custom-band assembly, not yet offered as `/create-band` Step 1 choices):**

| Name | Node ID | Notes |
|---|---|---|
| Headline Section - Top Left | `2951:68362` | Eyebrow + headline + subhead + CTA, left-aligned, narrow column width (matches Grid/Media Layout's copy-block width). |
| Headline Section - Top Right | `2951:68374` | Intentionally identical content/alignment to Top Left (confirmed 2026-09-14) — "Left"/"Right" names which side of a 2-column composition it sits on, not a distinct alignment treatment. Do not diverge these without an explicit decision to do so. |
| Headline Section - Top Center | `2951:68258` | Wider, centered variant — different alignment treatment, not just a repositioned copy of Top Left/Right. |
| Card Grid - 2 Column - Left or Right | `2951:68385` | Icon + title + description cards, matches the Grid template's card shape. |
| Card Grid - 4 Column - Center | `2951:68162` | Icon + value + label stat cards, matches the narrow band's shape — a genuinely different card shape from the 2-column one, not a column-count variant of it. |
| Content with CTA - Bottom Center | `2951:68335` | Body copy + CTA button, separable from the Headline Section so a composition can place it independently (e.g. below a card grid, not glued under the headline). |
| Media Frame | `2982:33` | Video/image placeholder, extracted from the Media Layout template's `Video` frame. Its icon renders as a red play glyph, matching the master exactly — this is the template's own icon design, unrelated to the background-follows-band-color rule (`0006`), which governs the frame's fill only. |
| Footer Nav Column | `2982:36` | One nav column (label + link list), extracted from the `lg` footer's "Resources" column. Text is white, styled for the dark footer background it came from — it will render as nearly invisible on Figma's default white canvas; that's correct, not a defect, and needs a dark background behind it to preview properly. |
| Chip | `2982:43` | Extracted from the narrow band's canonical chip (`0008`'s unified spec: `text-sm py-0.5 px-3 leading-tight`). Previously only existed embedded inside the 4-Column stat cards — this is the first version droppable onto any card independently, closing the gap that required manually cloning it ad hoc during the custom-band test. |

Found and fixed while cataloguing these (2026-09-14): Top Left's and Top Right's
eyebrow text was `CENTER`-aligned against a `LEFT`-aligned headline/subhead below
it — an internal mismatch by `decisions/0012`'s alignment rule. Fixed on both new
components. **The same mismatch exists on both original master templates**
(Grid's and Media Layout's eyebrows are also `CENTER` against `LEFT`)  — left
untouched per this ADR's read-only rule; fixing it there is a template revision
requiring its own explicit go-ahead, not a side effect of this catalog update.

The workflow for producing a new band design is:

1. The user selects a template by name (e.g. "Feature Band - Media Layout -
   Content with Video").
2. The user supplies the content for that instance: copy and images.
3. Claude creates a new frame elsewhere in the file, derived from the named
   template, populated with the supplied content.

Everything structural in the new frame — border, buttons, layout/grid, spacing,
type styles, color tokens — comes from the chosen template as-is. Claude does not
re-derive, restyle, or reinterpret any of it; only the content slots (headline,
body copy, CTA label, icon glyphs, image/video) are supplied per instance.

Any change to a template's structural properties is a template revision, not a
content request, and is done directly on the master frame itself — never as a
side effect of generating an instance (superseding this ADR only if the change is
itself a design decision worth recording).

## Alternatives considered

**Freeform regeneration per request.** Rejected — fastest short term, but produces
visible drift band to band, undermining the "template" premise and creating rework
to reconcile designs later.

**True Figma component + instance/variant system.** Deferred, not rejected —
instances with overridable content slots would enforce this automatically at the
tool level rather than by convention. Originally not adopted because band
variants beyond the first two hadn't been defined; that condition is now met (a
third full template plus six reusable components exist as of 2026-09-14), but
the deferral stands anyway — confirmed 2026-09-14 that plain frame-cloning stays
the mechanism for now, since it's the same proven approach the rest of the
project already relies on. Revisit only if frame-cloning actually becomes
insufficient, not simply because more pieces now exist.

## Consequences

- New band pages/campaigns can be generated quickly: the user names a template and
  supplies copy/images, nothing else, and gets a consistent result.
- As of 2026-09-14, `/create-band`'s `SKILL.md` needs an edit to add the third
  full template as a Step 1 choice — not done as part of this ADR update, tracked
  as a direct follow-up.
- The six reusable components are not yet consumed by anything — they exist as a
  catalogued, read-only reference for the custom-band assembly work still being
  designed. Nothing currently clones them automatically.
- Templates stay stable over time — they are read-only reference material, never
  touched by an instance-generation request. Anyone auditing the Templates page
  later can trust it still reflects the original, deliberate design.
- A genuine structural need (e.g. a third band layout, or a fix to the existing
  two) must be raised explicitly and lands as an edit to the master template frame,
  done as its own action — never as a side effect of a "create an instance" request.
- Open risk: without real Figma component instances, nothing at the tool level
  physically prevents Claude from editing a template frame by mistake while
  building an instance nearby. This ADR is a process rule, not an enforced
  constraint — verify after each generation that the named template frame is
  byte-for-byte unchanged, until instancing is adopted.

## Related

- Figma: Templates page, node-id `2683-14127` — "Feature Band - Media Layout -
  Content with Video" (node `2683-82644`) and "Feature Band - Grid - Offset with
  Benefit list" (node `2683-82662`).
- File: `NrSdzK3P0OzjKKNFKmuYmu` (Owens OnLine Homepage Design).
- Skill: `.claude/create-band/SKILL.md` — implements this workflow as an
  interactive `/create-band` flow (ask band type → ask content fields for that
  type → duplicate template → populate content only).
