# 0008. Layout, spacing, and type rules align to Tailwind's default scale; Tailwind wins on conflict

Status: Proposed
Date: 2026-09-10

## Context

A "Design Rules" audit page (extracted live from the real "Owens OnLine Homepage"
frame, `2492:30824`, cross-checked against Countries Page and Data Protection)
found the actual site does not follow one consistent layout/spacing/type system:
three different container widths, two disagreeing footer paddings, an asymmetric
footer, and several role-based type-size mismatches (see the "Design Rules" Figma
page, node `2799:3`, for the raw findings). Separately, `decisions/0001` already
established a precedent for the site's colors: snap to a Tailwind default stop
wherever the fit is good, keep custom only where there's a deliberate reason and
no token exists.

The same question needed answering for layout/spacing/type: several shipped
values have no valid Tailwind equivalent at all — off the 4px base grid entirely,
or a max-width with no close default stop. Asked to check the raw audit against
Tailwind's standards and, on conflict, prioritize Tailwind, producing a second
Figma page ("Design Rules — Tailwind Aligned", node `2806:3`) reconciling the two.

## Decision

Wherever a shipped value conflicts with Tailwind's default scale, Tailwind wins.
A "conflict" means either: the value sits off Tailwind's 4px base spacing grid
entirely, or a max-width has no reasonably close default stop. Where two
candidate values for the same visual role are BOTH valid Tailwind tokens, that is
a genuine design decision, not a Tailwind conflict — those are left open below,
not resolved by this ADR.

**Resolved (Tailwind-aligned):**

| Was | Now | Reasoning |
|---|---|---|
| Container: 1454/233, 1768/76, and 1880-content/20-margin (three systems) | `max-w-screen-2xl` (1536px), `mx-auto`, `px-8` gutter | None of the three shipped widths are Tailwind max-width tokens; two of the three margins (233, 76-as-a-named-step) aren't valid named spacing tokens either. 1536 is Tailwind's largest default container stop. At a 1920 canvas this gives a 192px margin each side — exactly `spacing-48`, an exact token. |
| Footer padding: 76px (homepage) vs. 154px (Countries Page, Data Protection) — internally disagreeing, neither a valid token | Same standardized container as everywhere else (`max-w-screen-2xl` / `px-8`) | Both shipped values were wrong independently of each other; the fix is to stop treating the footer as a bespoke container at all. |
| Footer bottom padding: 57px (asymmetric vs. 96px top) | 96px top AND bottom (`py-24`) | 57px is off the 4px grid outright (14.25×4); symmetric 96/96 matches the homepage's primary vertical-rhythm tier and removes an unexplained one-off. |
| Full-bleed grid variant margin: 20px | Gutter standardized to `px-8` (32px); the "no max-width cap" behavior itself is kept | 20px (`spacing-5`) was already a valid token, so this is a consistency change, not a conflict fix — done to match the gutter used everywhere else in the system. |
| Mobile band vertical padding: 96px (`py-24`), inherited unchanged from desktop | 48px (`py-12`) on the base/mobile viewport | 96px is a valid token but oversized for a 375px viewport by mobile-first convention (base `py-12`, scaling up via `md:`/`lg:` prefixes to `py-16`/`py-24`). This is a *breakpoint*-dependent tier, distinct from the existing 96/64 *page-context* tiers below — applied to the narrow band, wide band, and footer in the mobile mockup; Hero was already 0/0 (a custom composition) and unaffected. |

**Confirmed already Tailwind-native, no change:**

- Vertical rhythm: 96px (`py-24`, homepage default) and 64px (`py-16`, interior-page
  CTA/"Whats New" bands) are both exact tokens — kept as two deliberate rhythm
  tiers, not reconciled to one value, since neither conflicts with Tailwind. (See
  the Resolved table above for the third, breakpoint-dependent 48px mobile tier
  added after building the base/mobile mockup — that one *did* require a change,
  since 96px had been carried over unchanged rather than deliberately chosen.)
- Every measured type size (14, 16, 18, 24, 48, 60px) is an exact Tailwind step
  (`text-sm` / `text-base` / `text-lg` / `text-2xl` / `text-5xl` / `text-6xl`).
- Button spec (`h-10 px-6 py-2 gap-1 rounded-sm`) and Card spec
  (`p-6 gap-6 rounded-lg`) are already 100% Tailwind-native.
- Button — responsive (added 2026-09-16): the real `Buttons` component set
  (`Components` page, node `8:1357`) already ships 5 real `Size` variants
  (`Navigation`, `base`, `sm`, `xs`, `l`), and measuring all of them found
  every one sits exactly on Tailwind's grid already — no conflict to
  reconcile, just a size to pick per breakpoint. `l` (`h-10 px-6 py-2 gap-1
  rounded-sm text-base`) is the size already used everywhere and matches
  the spec above exactly. `base` (`h-9 px-6 py-2 gap-1 rounded-sm
  text-sm`) is the natural mobile counterpart — identical padding, gap, and
  radius, only height and font-size step down, the same "shrink size and
  type, keep everything else" pattern `0009` already established for
  headlines. `sm`/`xs` are a separate, tighter-padding (`px-3`) variant, not
  used by this project's CTA buttons, and are functionally identical to
  each other. A sixth size, `Navigation` (66px tall, 15px padding, 0
  radius), was checked too and ruled out entirely — it's a top-nav link,
  not a CTA button, and isn't Tailwind-aligned at all; it doesn't belong in
  this scale.

  Checked against Tailwind's full default breakpoint set (`base`, `sm`
  640px, `md` 768px, `lg` 1024px, `xl` 1280px, `2xl` 1536px), not just the
  base/lg pair used elsewhere in this project — only 2 real size changes
  exist across all 6, which is normal Tailwind practice (a class only needs
  a breakpoint prefix where a real change happens; the rest cascade):

  | Breakpoints | Size used | Classes |
  |---|---|---|
  | `base`, `sm`, `md` (0–1023px) | `base` | `h-9 px-6 py-2 gap-1 rounded-sm text-sm` |
  | `lg`, `xl`, `2xl` (1024px+) | `l` | `h-10 px-6 py-2 gap-1 rounded-sm text-base` |

  Resolved spec: `h-9 px-6 py-2 gap-1 rounded-sm text-sm lg:h-10
  lg:text-base` (padding/gap/radius constant across every breakpoint, only
  height and font-size step down below `lg`). Documented live with real
  Default/Hover/Down instances for both sizes on the "Reusable Components"
  page (node `3019:2`).

  **Full spec audit (added 2026-09-16)** — padding, gaps, and icon sizes,
  checked against the actual rendered geometry (icon/text edge positions),
  not just the reported properties, per `0012`'s discipline:
  - Padding: `px-6 py-2` (24px/8px), exact and symmetric, both sizes.
  - Gap between icon and text (both sides): exactly 4px (`gap-1`), both
    sizes, all 3 Behavior states — verified by position math, not just
    `itemSpacing`.
  - Font family: was Neue Haas Grotesk Text Pro (75 Bold), not Inter — the
    same live-homepage-needs-migration gap already documented under Font
    Family in `context/tailwind-tokens.md`, now confirmed to specifically
    include this Button component. **Migrated 2026-09-16** on the same 6
    audited variants to Inter Bold — verified clean on the documentation
    page and propagated correctly to the real Grid template's live CTA
    instance, no clipping or reflow issues. The other 46 Type/Band/Size
    combinations in the `Buttons` set were not touched — same likely gap,
    unmigrated.
  - Icon sizes: were **not governed by any size token at all** — raw vector
    bounding boxes, ~11.5–11.7px on `l` (between Tailwind's `w-2.5`/10px and
    `w-3`/12px stops) and ~10.0–10.2px on `base` (close to but not exactly
    `w-2.5`). Resolved: snapped to `w-3 h-3` (`l`) and `w-2.5 h-2.5`
    (`base`) on the 6 variants actually in use and documented on the
    "Reusable Components" page (Get Demo/Light, both sizes, all 3
    Behaviors) — **this edits the real master component**, so the fix
    propagates to every existing instance across the file (verified against
    the Grid template's live CTA button, renders correctly, no distortion).
    The other 46 Type/Band/Size combinations in the `Buttons` set were not
    checked or touched — same likely gap, unaudited.
  - Margins: not an intrinsic Button property — external spacing around a
    button instance is governed per-band by its parent stack's own
    `itemSpacing`, already documented elsewhere.

**Get Demo button and standalone Chip were also raw, unresolved blue
(added 2026-09-17)** — a more significant gap than the amber one, since
Get Demo is this project's most-used, most-documented button. Its Default
fill (`#286BE0`) and the standalone Chip's border/text color were both
exact matches to `0001`'s *raw* "Action Blue" — never actually migrated to
the resolved `blue-600` (`#2563EB`) anywhere, despite `0001` deciding this
weeks ago. Found only by deliberately sweeping every color-bearing element
against `0001` rather than assuming the amber gap was the only one.
- **Get Demo, both sizes, all 3 states, fixed:** Default `bg-blue-600`
  (`#2563EB`), Hover `bg-blue-500` (lighter step), Down `bg-blue-700`
  (`#1D4ED8`) — Down now exactly matches `0001`'s separately-resolved
  "Link Blue"→`blue-700`, a clean confirmation this is the right family of
  values rather than an invented scale.
- **Chip border/text, fixed:** `border-blue-600 text-blue-600`.
- This raises the obvious follow-up question: **are there other raw,
  uncaught colors elsewhere in the reusable components?** Only Button and
  Chip have been swept for this specific failure mode (raw-vs-resolved) so
  far; Card, Media Frame, and Footer pieces have not been re-checked
  against `0001` specifically (only against `0008`'s spacing/type/icon
  rules). Worth a dedicated pass before considering color "done."

**Get Demo, Band=Dark (added 2026-09-18)** — a white/outline button for use
on dark-background bands. `Band=Dark` already existed as a concept on the
real `Buttons` set, already populated for `Sign Up`/`Secondary`, but never
for `Get Demo` — this project's actual CTA type, used in every band built
so far. Rather than inventing a new pattern, replicated Sign Up's existing
Band=Dark structure exactly: Default is an **outline** button (`fill:
visible=false`, 2px white border bound to the `white` variable, white
text/icons) so the dark band shows through; Hover fills solid white with
navy (`#0A2540`) text/icons; Down fills a light blue tint (`#E7F0FF`) with
the same navy text/icons. `#0A2540` is reused as-is from the existing Sign
Up Dark pattern, same treatment as `0001`'s deliberately-kept-custom Ink/
Brand Navy colors — not forced onto a Tailwind stop.

Two real bugs caught and fixed during the build, not in the final result:
1. `figma.createAutoLayout()`'s wrapper frames default to an **opaque
   white fill** — never cleared, this hid the intentionally-transparent
   Default button behind a solid white rectangle and made its white label
   text invisible (white-on-white). Fixed by explicitly clearing the
   wrapper frames' fills.
2. The cloned icons kept their **source white fill**, invisible against
   the new white/light-blue Hover/Down backgrounds. Fixed by setting icon
   fills to match each state's text color (navy).

Both sizes (`l`/`base`) built and verified — Default via a temporary dark
backdrop added to the "Reusable Components" page (since a transparent
button can't be verified on Figma's default white canvas), Hover/Down via
direct fill/icon-color data checks. Master components' inherited
annotations from cloning (stale references to Light-band blue values) were
also cleaned up, not left stale.

**Changed 2026-09-18: outline dropped, solid white throughout.** Per
direct instruction, this button is no longer an outline at Default — the
whole three-state design was redone:
- Default: `bg-white`, navy (`#0A2540`) text/icons (previously white
  text/icons on a transparent fill with a white border).
- Hover: `bg-blue-50` (`#EFF6FF`) — real v4 OKLCH-derived value.
- Down: `bg-blue-100` (`#DBEAFE`) — real v4 OKLCH-derived value, replacing
  the ad hoc `#E7F0FF` borrowed from Sign Up/Secondary's Down state.
Text/icons stay navy across all 3 states now, not just Hover/Down, since
white text would be invisible against the new solid-white Default.
Suggested rather than guessed: Hover/Down step through the real Tailwind
blue scale (white → 50 → 100), the same "step to an adjacent named shade"
convention already established for every other multi-state color in this
project (Get Demo, Cancel), rather than an arbitrary tint.

**Button set pruned to the indicated-reusable subset (added 2026-09-21)** —
the real `Buttons` component set had grown to 66 variants across 6 Types,
most never touched or documented by this project. Removed 40, kept 26:
- Removed entirely: all `Sign Up` (6, reference pattern only, never
  adopted), all `Secondary` (18, same), all `Desktop`/`Navigation` (4,
  already ruled out in this ADR as not a CTA button), Get Demo `sm`/`xs`
  (6, already documented as unused by this project's CTA buttons).
- Removed selectively: Find Products' `sm`/`xs` sizes (6, Active/Hover/Down)
  — but its `l`/`base` Active/Hover/Down were **kept**, not removed, per
  explicit confirmation: those pre-existed, were never touched, but
  deleting them would leave Find Products with a Default and no working
  interaction states — a functional regression, not a cleanup.
- Kept: Get Demo Light+Dark (`l`/`base`, 12), Find Products `l`/`base`
  Default+Active+Hover+Down (8), Cancel `l`/`base` (6) — 26 total.
- Checked for live instances on the real homepage before deleting anything
  (zero matches) — not an exhaustive every-page check, flagged as such
  rather than claimed as certain.
- Remaining 26 rearranged into a clean grid (grouped by Type → Band → Size
  rows → Behavior columns). Caught and fixed a real bug in the process: the
  component set's own frame doesn't auto-resize when children are
  repositioned outside its stored bounds, and `clipsContent: true` silently
  clipped the Find Products "Active" column until the frame was manually
  resized to fit.
- **SOP note**: this cleanup, done under `0019`'s 5-tool-call window
  constraint, actually took 6 calls — the clipping bug needed 2 extra
  diagnose-and-fix calls beyond the plan. Flagged here as real evidence for
  calibrating that cap, not silently absorbed: a verification step that
  finds a genuine bug will often need at least one more call to fix it,
  and 5 may not leave room for that in practice.

**Colored status chips (added 2026-09-17)** — a second real chip family
exists beyond the standalone Chip: colored status pills in the wide band
("Certified" ×3 per breakpoint, green; "In Progress" ×1 per breakpoint,
amber), on both mobile and `lg`. Their size/padding/font were already
correct from the prior chip-unification pass (`text-sm py-0.5 px-3
leading-tight`, verified again here) — only their *colors* were still raw,
pre-`0001`-resolution values, never actually migrated anywhere:
- **Green "Certified" (6 instances, both breakpoints): fully resolved and
  fixed.** Background `#D1F0D5`→`bg-green-100` (`#DCFCE7`), dot
  `#22A55B`→`green-600` (`#16A34A`), text `#0E5A1C`→`text-green-800`
  (`#166534`) — all three were exact matches to `0001`'s *raw* Green Tint/
  Verify Green/Green Ink values, already fully decided there, just never
  applied. Zero judgment call — pure mechanical migration.
- **Amber "In Progress" (2 instances): partially resolved.** Text
  `#856210`→`text-amber-800` (`#92400E`) fixed, matching `0001`'s raw Gold
  Ink resolution. Background (~`#FFF6DC`) and dot (~`#FFB228`) are **not**
  in `0001`'s original 20-color catalog at all — no decided answer exists,
  left unchanged pending a color decision (candidates checked: background
  sits near `amber-50`, dot near `amber-400`, neither an exact match).
- Corner radius (15px) already renders identically to `rounded-full` at
  this height (22px) — any radius ≥ 11px produces the same visual capsule,
  so no visual fix needed there, just a class-hygiene note for later.

**Button color variants (added 2026-09-17)** — color is already an
independent `Type` variant axis on the real `Buttons` set, orthogonal to
`Size`/`Behavior`/`Band` — no separate button component is needed per
color, confirming the existing architecture is already correct. Auditing
the actual 52 (now 60) variants surfaced two real gaps:
- **"Find Products" (red, `bg-red-700`-ish exact `#D82729`, matching
  `CLAUDE.md`'s reserved Find Products red almost exactly) was missing its
  Default state** — only Active/Hover/Down existed. Created Default (`l`
  and `base` sizes) from Active's color, which already matched the brand
  red.
- **A "Cancel" (gray) button didn't exist in the component set at all**,
  despite being a real button in use (`#3F4C5D`). Cross-checking
  `decisions/0001` first (a step skipped initially, then corrected) showed
  this hex is already the named, catalogued "Slate" color, already
  resolved there to snap exactly to `slate-600` — not a new custom color
  needing invented shades. Created as a new `Type=Cancel` (`l`/`base`
  sizes, Default/Hover/Down): `bg-slate-600` (Default), `bg-slate-500`
  (Hover, lighter step), `bg-slate-700` (Down, darker step) — real adjacent
  Tailwind stops in the same family, matching Get Demo's own
  lighter-on-hover/darker-on-down direction rather than an invented
  formula.
- Icons on Cancel intentionally kept as the inherited search+arrow icons
  from Get Demo, despite not being semantically ideal for a cancel action —
  explicit user call, not an oversight.
- `sm`/`xs` sizes were not created for either addition — scope matches
  this project's own `l`/`base` usage, consistent with every other fix in
  this ADR.

**Card and Chip full spec audit (added 2026-09-16)** — same rigor applied
to the "Card Grid - 2 Column" reusable component (`2951:68385`) and the
standalone Chip (`2982:43`):

- **Chip: already 100% compliant**, no fixes needed — `px-3 py-0.5` exact,
  `rounded-full`, Inter Semi Bold `text-sm`, `leading-tight` (125%,
  percent-based). This one had already been through a prior fix pass.
- **Card, finding 1 — line-height was fixed-pixel, not percent**: title
  (Inter Tight Bold 24px) had a hardcoded 32px line-height, description
  (Inter Regular 16px) a hardcoded 24px — the same brittle anti-pattern
  `0009` already eliminated from headlines. Both values turned out to
  already equal Tailwind's own default ratios for `text-2xl` (32px = 133%)
  and `text-base` (24px = 150%) — so this was a pure unit-type conversion,
  zero visual change, not a value change. Fixed on all 6 cards.
- **Card, finding 2 — icon was ungoverned and irregularly shaped, not just
  off-grid.** Unlike the Button icons (simple, near-square vectors), the
  Card icon (`0003`'s canonical target-and-arrow glyph) has a genuinely
  non-square natural bounding box — the arrow extends past the circle —
  and heights were consistent (~53.83px) while widths varied 44.75–59.3px
  across the 6 different icon glyphs actually in use. Forcing an exact
  square resize would visibly distort the circle into an oval. Resolved:
  wrapped each icon in a fixed `w-16 h-16` (64px) container, centered,
  icon art itself untouched — 64px was chosen as the smallest Tailwind
  stop that fits the widest icon (59.3px) without clipping. This is the
  standard pattern going forward for any icon whose natural shape isn't
  square: a fixed-size container is the Tailwind-aligned token, not the
  glyph itself.
- Both fixes applied to all 6 cards in the reusable component (not just
  one), and documented live on the "Reusable Components" page (`3019:2`).
  The original Grid master template (`2683:82662`) was not touched — same
  line-height and icon-sizing gaps presumably still exist there, unaudited,
  consistent with this project's read-only-master rule.

**Media Frame full spec audit (added 2026-09-16)** — no real component set
exists for this element either (checked and ruled out a false-positive
match, "Due Diligence & Media," which is an unrelated service-category
name). Audited the extracted reusable component (`2982:33`):
- Corner radius was 5px, off-grid (exactly between `rounded`/4px and
  `rounded-md`/6px — a genuine tie, not resolvable by nearest-stop alone).
  Snapped to `rounded-lg` (8px) as a judgment call for consistency with
  Card's content-container radius, not a data-forced snap like the other
  fixes in this ADR — flagged as such rather than presented as equally
  certain.
- Icon (play badge) was 78×53.625px, off-grid. Unlike Card's icon, this one
  is a single simple `VECTOR`, not a multi-part composite, and it's
  intentionally rectangular (a play-button badge, not meant to be square)
  — so it was resized directly to `w-20 h-14` (80×56px, ~3% change) rather
  than wrapped in a container. Confirmed via screenshot: no visible
  distortion.
- No font/text on this element — nothing to check there.
- The icon node itself cannot carry an annotation (`VECTOR` type, same
  restriction as Button's icons) — documented on the parent Media Frame
  instead.
- Chip spec (added 2026-09-11): `text-sm` (14px), `py-0.5 px-3` (2px vertical /
  12px horizontal), `leading-tight` (125%) — every value is an exact Tailwind
  token. Standardized across both chip families found in the mockups (the
  narrow-band CTA-style pills and the wide-band status pills), which had
  drifted from each other before this: CTA pills ran 14–16px text with
  8px/16px padding, status pills ran 12–14px text with 2px/12px padding
  already close to the target. All 16 chip instances across mobile and `lg`
  now match exactly.
- The line-height fix was a second pass, same root cause as `decisions/0009`'s
  headline bug: every chip still carried a *fixed-pixel* line-height (16px,
  20px, or 24px) left over from before its font size was unified to 14px, so
  ratios ranged 1.14×–1.71× depending on which chip happened to start where.
  First tried `leading-none` (1.0×, the tightest standard choice for
  single-line UI text) — this uniformly compacted all 16 chips to 18px, but
  at exactly 1.0× there's zero slack in the line box, and descenders on
  lowercase `g`/`y`/`p`/`q`/`j` visibly crowded or touched the chip's bottom
  border (Inter's real glyph metrics exceed its nominal em-box, so `1.0×`
  isn't actually safe for descenders the way the ratio implies). Stepped up
  one notch to `leading-tight` (1.25×) — the same value already used for
  headlines — which gives descenders clear breathing room while staying far
  tighter than the original 1.14×–1.71× spread. Final chip height: a uniform
  22px (up from 18px, still well below the original 20–28px range).

**Explicitly NOT resolved by this ADR — Tailwind has no opinion:** the five
items originally listed here (eyebrow size, card-description size, page-heading
size, system font family, and the full-bleed container's continued existence)
were left open by design, since Tailwind doesn't arbitrate between two
otherwise-valid tokens. All five have since been decided by `decisions/0013` —
see that ADR for the resolutions and reasoning. This section is kept as a
pointer rather than deleted, since it's the reason `0013` exists.

## Alternatives considered

**Keep the shipped values as the ratified spec, treat Tailwind alignment as
aspirational only.** Rejected — this is what `decisions/0004` already did for the
two band-template literal colors (`#F1F5F9`, `#F4F7FC`), and it was the right call
there because neither had a design-system token to defer to. Here, Tailwind's
spacing and max-width scale already exist and are the presumed implementation
target; refusing to name the mismatch would mean treating known-arbitrary values
(154px, 57px, 233px) as if they were intentional.

**Snap every value to the nearest token even where two tokens are both plausible
for the same role (e.g. force eyebrow to one size).** Rejected — Tailwind doesn't
have an opinion on which of two valid tokens is "more correct" for a given design
role; picking one would be a design decision disguised as a Tailwind-compliance
fix. Kept explicitly open instead.

**Apply this ADR's container change directly to the two `/create-band` master
templates as part of writing it.** Rejected for now — `decisions/0002` treats
structural template changes as a deliberate, explicit action, never a side effect
of another decision. 1536 is 344px narrower than the widest shipped container
(1880) and will visibly reflow hero/CTA/footer content; that needs its own
sign-off, separate from ratifying the rule itself.

## Consequences

- The "Design Rules — Tailwind Aligned" Figma page (`2806:3`) becomes the
  canonical layout/spacing/type reference going forward, superseding the raw
  "Design Rules" audit page (`2799:3`) for anything it explicitly resolves; the
  audit page remains as the historical record of what was actually shipped at
  the time it was measured.
- The container-width change is not yet applied anywhere — not to the master
  templates, not to any live page. This ADR ratifies the rule; applying it to the
  two `/create-band` templates (and to the real homepage/interior pages) is
  separate follow-up work requiring its own explicit go-ahead, given the visual
  reflow impact.
- Five of the eight original "Open Questions" from the raw audit are resolved
  directly by this ADR (container system ×2, footer padding, footer symmetry,
  full-bleed gutter). The remaining three (eyebrow size, card-description size,
  font family) plus two more this ADR itself introduced as open (page-heading
  size, full-bleed's continued existence) were all subsequently resolved by
  `decisions/0013`.
- Any future layout/type value pulled from the live site should be checked
  against this ADR's table before being treated as "the rule" — if it disagrees,
  this ADR wins per `decisions/README.md`'s drift policy, and the Figma page's
  rationale should cite this ADR rather than restate it.

## Related

- `decisions/0001-tailwind-alignment-homepage-colors.md` — the color-system
  precedent this ADR mirrors for layout/spacing/type.
- `decisions/0002-band-template-style-lock.md` — why the container-width change
  isn't applied to the master templates as part of this ADR.
- `decisions/0004-band-background-and-card-color.md`,
  `decisions/0006-media-layout-video-placeholder-color.md`,
  `decisions/0007-tailwind-class-annotations-on-band-nodes.md` — prior
  `/create-band` color/Tailwind decisions this sits alongside.
- `decisions/0009-responsive-type-scale.md` — the sibling breakpoint-dependent
  rule for font size; the mobile vertical-padding tier added here follows the
  same "some values change by viewport, most don't" pattern.
- `decisions/0012-responsive-text-container-audit.md` — the mobile padding
  reduction was verified using that ADR's "check the actual rendered result,
  not just the property" discipline.
- `decisions/0013-role-size-and-font-family-resolutions.md` — resolves all
  five items this ADR left open above.
- Figma: "Design Rules" page (raw audit, node `2799:3`), "Design Rules —
  Tailwind Aligned" page (this ADR's source, node `2806:3`), and "Owens OnLine
  Homepage — base/mobile (375px)" (`2834:47722`, where the mobile padding tier
  was applied), all on file `NrSdzK3P0OzjKKNFKmuYmu`.
