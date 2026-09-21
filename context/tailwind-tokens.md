# Tailwind Tokens — Developer Reference

This is the single place to look up which Tailwind class to use for any
color, spacing, type, or component value already decided for this project.
It's the code-facing counterpart to the "Design Rules — Tailwind Aligned"
Figma page (node `2806:3`) — that page is the designer-facing view of the
same values; this file is meant to be grepped from an editor.

**This file documents the *result*. The `decisions/` ADRs document the
*reasoning*.** If this file and an ADR ever disagree, the ADR wins — fix this
file to match it, don't re-derive the value here. Every row below cites the
ADR that decided it; read that ADR if you need the "why," not just the "what."

**Keep this updated:** whenever a new ADR establishes or changes a token,
add or update its row here in the same change — this file goes stale the
moment a decision lands only in an ADR and never gets copied over.

**Legend:** 🟡 = open conflict, not yet resolved — two valid values compete
for the same role; do not silently pick one. See the linked ADR.

---

## By Category

### Font Family

| Family | Used on | Source |
|---|---|---|
| Inter / Inter Tight | System font — ratified target for the whole site | `0013` |

The two `/create-band` master templates already spec this; the live homepage
currently ships Neue Haas Grotesk Text Pro instead and needs to migrate — that
migration is separate, unscoped follow-up work, not yet done. Known tooling
gap: Inter Tight isn't loadable in this Figma environment, so any Figma work
on a role that calls for Inter Tight uses `{ family: 'Inter', style: 'Bold' }`
as a stand-in — state this explicitly whenever it's used, never silently.

### Color

| Class | Value | Used on | Source |
|---|---|---|---|
| `bg-white/60` | `#FFFFFF` @ 60% opacity | Hero content-wrapper scrim, sitting over the background photo for text contrast — base/mobile only, `lg`/2xl stay untinted | user, 2026-09-11 |
| `bg-white` | `#FFFFFF` | Band background — White option | `0007` |
| `bg-[#F4F7FC]` | `#F4F7FC` | Band background — Slate Gray option (no token exists for this yet — arbitrary value is deliberate, not a gap to silently fix) | `0004`, `0007` |
| `bg-slate-100` | `#F1F5F9` | Card fill, paired with a White band | `0001`, `0007` |
| `bg-white` | `#FFFFFF` | Card fill, paired with a Slate Gray band | `0007` |
| `bg-gray-100` | `#F3F4F6` | Video placeholder frame, paired with a White band | `0006`, `0007` |
| `bg-white` | `#FFFFFF` | Video placeholder frame, paired with a Slate Gray band | `0007` |
| `text-blue-600` / `border-blue-600` / `bg-blue-600` | `#155DFC` (real v4 OKLCH, not v3's `#2563EB`) | Action Blue (primary CTA color family) — Get Demo button Default state and Chip border/text. Migrated from raw `#286BE0` 2026-09-17, then from v3 hex to real v4 OKLCH 2026-09-18 | `0001`, `0008` |
| `text-blue-700` | `#1D4ED8` | Link Blue (hover/active step) | `0001` |
| `bg-blue-50` | `#EFF6FF` | Pale Blue backgrounds | `0001` |
| `text-slate-600` | `#475569` | Slate (secondary text family) | `0001` |
| `text-slate-700` | `#334155` | Navy Gray | `0001` |
| `text-slate-500` / `border-slate-500` | `#64748B` | Border Strong — exact match, unchanged | `0001` |
| `text-green-600` | `#16A34A` | Verify Green | `0001` |
| `bg-green-100` | `#DCFCE7` | Green Tint | `0001` |
| `text-green-800` | `#166534` | Green Ink | `0001` |
| `text-amber-800` | `#92400E` | Gold Ink | `0001` |
| `bg-amber-100` | — | Cream / Cream Glow (merged into one token) | `0001` |
| *(custom, no token)* | `#2A3133` | Ink — primary heading/body text. Deliberately kept off-scale; do not snap this to a Tailwind gray/zinc/slate step | `0001` |
| *(custom, no token)* | — | Brand Navy / Band Navy (merged pair) | `0001` |
| `bg-slate-600` / `bg-slate-500` / `bg-slate-700` | `#45556C` / `#62748E` / `#314158` (real v4 OKLCH, not v3 hex) | Cancel button — Default/Hover/Down. Same "Slate" color already catalogued above, now also used as a background (not just text) | `0001`, `0008` |
| `bg-red-700`-ish (exact `#D82729`) | `#D82729` | Find Products button — matches `CLAUDE.md`'s reserved Find Products red almost exactly; not part of the original homepage 20-color extraction, sourced from the real Buttons component set instead | `0008` |

### Container & Layout

| Class | Value | Used on | Breakpoint | Source |
|---|---|---|---|---|
| `max-w-screen-2xl` | 1536px | Page content container | `lg` and up | `0008` |
| `mx-auto` | — | Page content container centering | all | `0008` |
| `px-8` | 32px | Container gutter | `lg` and up | `0008` |
| `px-4` | 16px | Container gutter | base/mobile | `0008` |
| `w-full` (no max-width) | — | Full-bleed grid variant | all | `0008` |

### Vertical Rhythm (band padding, `py-*`)

| Class | Value | Used on | Breakpoint | Source |
|---|---|---|---|---|
| `py-24` | 96px | Homepage band default | `lg` / 2xl | `0008` |
| `py-16` | 64px | Interior-page CTA / "Whats New" bands | all (page-context tier, not breakpoint) | `0008` |
| `py-12` | 48px | Any band, mobile | base/mobile | `0008` |

### Type Scale (`text-*`)

| Class | Value | Used on | Breakpoint | Source |
|---|---|---|---|---|
| `text-sm` | 14px | Eyebrow — Hero and Section, unified | all | `0008`, `0013` |
| `text-base` | 16px | Button label, Footer nav label, Card description (all cards) | all | `0008`, `0013` |
| `text-lg` | 18px | Body/Subheadline | all | `0008` |
| `text-2xl` | 24px | Card / feature title | all | `0008` |
| `text-3xl` | 30px | Hero headline, Section/CTA headline | base/mobile | `0009` |
| `text-4xl` | 36px | Interior page H1 | base/mobile | `0009` |
| `text-5xl` | 48px | Hero headline, Section/CTA headline | `lg` / 2xl | `0008`, `0009` |
| `text-6xl` | 60px | Interior page H1 | `lg` / 2xl | `0008`, `0009` |

Eyebrow and card-description sizing conflicts noted in `0008` are resolved by
`0013`: both unified to a single size across every instance. One caveat on
card description: a "product card" component elsewhere on the real site
(not yet touched by any mockup here) still ships at 18px — `0013` sets the
target for it at 16px, but migrating it is separate follow-up work, not done
yet. Page-heading size (`text-5xl` homepage vs. `text-6xl` interior) is *not*
a conflict — `0013` ratified these as two intentionally different roles.

### Line-height (`leading-*`)

| Class | Value | Used on | Breakpoint | Source |
|---|---|---|---|---|
| `leading-tight` | 125% | Hero headline, Section/CTA headline, Interior H1 | base/mobile | `0009` |
| `leading-none` | 100% | Hero headline, Section/CTA headline, Interior H1 | `lg` / 2xl | `0009` |

Applies to the three headline roles only. Every other text role has no
documented line-height override — leave at the browser/Tailwind default for
that `text-*` size.

### Letter-spacing (`tracking-*`)

| Class | Value | Used on | Breakpoint | Source |
|---|---|---|---|---|
| `tracking-tight` | -2.5% (-0.025em) | Hero headline, Section/CTA headline, Interior H1 | all | `0009` |

Headline roles only. Never apply negative tracking to body copy, eyebrows,
buttons, or any text below display size — see `0009`'s alternatives-considered
section for why.

### Border Radius (`rounded-*`)

| Class | Value | Used on | Source |
|---|---|---|---|
| `rounded-sm` | 2px | Button | `0008` |
| `rounded-lg` | 8px | Card | `0008` |

### Component Specs

| Component | Classes | Source |
|---|---|---|
| Button | `h-9 px-6 py-2 gap-1 rounded-sm text-sm` (`base`/`sm`/`md`) / `lg:h-10 lg:text-base` (`lg`/`xl`/`2xl`, cascades) | `0008` |
| Card | `p-6 gap-6 rounded-lg` | `0008` |
| Chip | `text-sm py-0.5 px-3 leading-tight` (14px text, 2px vertical / 12px horizontal padding, 125% line-height) | `0008` |

Chip applies to both chip families found in the mockups (narrow-band
CTA-style pills with an icon, wide-band status pills) — one spec for both,
no per-family variation. `leading-none` (1.0×) was tried first since chips
never wrap and have no multi-line readability tradeoff to protect — but at
exactly 1.0× there's zero slack for descenders, and lowercase `g`/`y`/`p`/`q`/`j`
visibly crowded the chip's bottom border. `leading-tight` (1.25×, same value
used for headlines) gives descenders clearance while staying much tighter
than the pre-fix values.

---

## Breakpoint Transform Table (lg → mobile)

**Purpose:** this is the one table a generation step (today's `/create-band`,
or the future single-band subagent) walks to derive a mobile (375px)
instance from an lg (1024px) instance mechanically, instead of a person
hand-building a second master per template. Every row is already decided
elsewhere — this table just compiles those deltas into a generation-ready
shape. See `0015` for why this exists as one table rather than duplicate
masters. Content (copy length, image visibility) is deliberately **not**
in this table — that's `0010`/`0011`'s per-band human judgment call, not a
mechanical lookup.

| Property | lg (1024px) | Mobile (375px) | Delta type | Source |
|---|---|---|---|---|
| Container gutter | `px-8` (32px) | `px-4` (16px) | value swap | `0008` |
| Container max-width | `max-w-screen-2xl` | `max-w-screen-2xl` (no cap change — gutter does the work) | no delta | `0008` |
| Band vertical padding — homepage tier | `py-24` (96px) | `py-12` (48px) | value swap | `0008` |
| Band vertical padding — interior tier | `py-16` (64px) | `py-12` (48px) | value swap | `0008` |
| Hero / Section / CTA headline size | `text-5xl` | `text-3xl` | value swap | `0009` |
| Interior Page H1 size | `text-6xl` | `text-4xl` | value swap | `0009` |
| Headline roles — line-height | `leading-none` (100%) | `leading-tight` (125%) | value swap | `0009` |
| Headline roles — tracking | `tracking-tight` | `tracking-tight` (unchanged) | no delta | `0009` |
| Eyebrow, Body, Card title/description, Chip, Footer nav label | same size/spec both breakpoints | same | no delta | `0008`, `0013` |
| Button | `l` size — `h-10 px-6 py-2 gap-1 rounded-sm text-base` | `base` size — `h-9 px-6 py-2 gap-1 rounded-sm text-sm` | height + font-size step down; padding/gap/radius unchanged | `0008` (added 2026-09-16, real component-set sizes, both exact Tailwind stops) |
| Grid-type band card layout | 2-column grid (`row` frames, 2 Cards each) | stacked single column, uniform `itemSpacing: 16` between every card (not the pre-fix 16/24/16 alternation) | reflow + gap normalization | `0012` (this session's `lg` audit), generalized by `0015` |
| Hero portrait photo | visible (`lg:block`) | hidden (`hidden` — decorative, not a parity violation) | visibility toggle, decorative assets only | `0010` |
| Chip spec (`text-sm py-0.5 px-3 leading-tight`) | same | same | no delta | `0008` |
| Card container (`p-6 gap-6 rounded-lg`) | same | same | no delta | `0008` |
| Footer content-gap / nav-list-item-gap / divider-copyright-gap | 80 / 16 / 48 | 80 / 16 / 48 (same both — these were pre-existing off-grid bugs on both breakpoints, now fixed uniformly, never a real breakpoint delta) | no delta | this session's `lg` audit, `0012` |

**Not yet in this table:** Interior Page H1 is marked 🟡 elsewhere in this
file as unverified against a real instance — treat its row above with the
same caveat until that's resolved.

---

## By Element

Everything that applies to one named element, in one place — for building
that element without cross-referencing the category tables above.

### Hero (container)
- Content wrapper background: `bg-white/60` scrim over the background photo —
  base/mobile only, deliberately not applied at `lg`/2xl → user, 2026-09-11
- Portrait photo: `hidden lg:block` — hidden on base/mobile, visible `lg` and
  up. Removing it is not a content-parity violation; see `decisions/0010`'s
  decorative-image carve-out. Saved 356px (~32%) of mobile Hero height.
  → `0010`

### Hero Headline
`text-3xl lg:text-5xl leading-tight lg:leading-none tracking-tight`
→ `0009`

### Section / CTA Headline
(e.g. "Not an algorithm. A network of people.", "Your data, protected.")
`text-3xl lg:text-5xl leading-tight lg:leading-none tracking-tight`
→ `0009`

### Interior Page H1
`text-4xl lg:text-6xl leading-tight lg:leading-none tracking-tight`
→ `0009` — 🟡 not yet verified against a real interior-page instance; treat as
recommended by extension from the other two headline roles, not confirmed.

### Hero / Section Eyebrow
`text-sm` — unified, every instance.
→ `0008`, `0013`

### Body / Subheadline
`text-lg` → `0008`

### Card / Feature Title
`text-2xl` → `0008`

### Card Description
`text-base` — unified. The real site's "product card" component still ships
at `text-lg` and needs migrating to match; not yet done.
→ `0008`, `0013`

### Button
`h-9 px-6 py-2 gap-1 rounded-sm text-sm` on `base`/`sm`/`md` (0–1023px),
`lg:h-10 lg:text-base` on `lg`/`xl`/`2xl` (1024px+, cascades — no separate
value needed at `xl`/`2xl`). Padding, gap, and radius stay constant across
every one of Tailwind's 6 default breakpoints; only height and font-size
step down below `lg`. Sourced from the real `Buttons` component set's
`base` and `l` `Size` variants (`Components` page, node `8:1357`) — both
already exact Tailwind stops, no reconciliation needed. A 6th size,
`Navigation`, was checked and ruled out — it's a top-nav link, not a CTA
button, and isn't Tailwind-aligned. Real Default/Hover/Down states for both
sizes are documented live on the "Reusable Components" page (`3019:2`).

Full spec, verified via rendered geometry: `px-6 py-2` padding exact both
sizes; icon-to-text gap exactly `gap-1` (4px) both sides, both sizes, all 3
states. Icon size — `w-3 h-3` (`l`) / `w-2.5 h-2.5` (`base`) — snapped to
these exact Tailwind stops 2026-09-16; previously ungoverned raw vector
bounding boxes (~11.5px / ~10px). Font family migrated 2026-09-16 from
Neue Haas Grotesk Text Pro to Inter Bold on the same 6 variants — the first
component in this project to actually complete the live-site font
migration noted under Font Family above, rather than just flag it.
→ `0008`

### Card (container)
`p-6 gap-6 rounded-lg`
→ `0008`
- Fill when paired with a White band: `bg-slate-100`
- Fill when paired with a Slate Gray band: `bg-white`
→ `0007`
- Icon container: fixed `w-16 h-16` (64px), centered, added 2026-09-16. The
  icon glyph (`0003`'s canonical target-and-arrow) is naturally non-square
  and varies 44.75–59.3px wide across the 6 real icons in use — the
  container is the Tailwind-aligned token, not the glyph itself, which
  stays untouched to avoid distorting it.
- Title line-height: `leading-[133.33%]` (exact Tailwind default for
  `text-2xl`) — was a hardcoded 32px, converted to percent 2026-09-16, no
  visual change.
- Description line-height: `leading-[150%]` (exact Tailwind default for
  `text-base`) — was a hardcoded 24px, same conversion, no visual change.

### Video Placeholder Frame
- Fill when paired with a White band: `bg-gray-100`
- Fill when paired with a Slate Gray band: `bg-white`
→ `0006`, `0007`
- Corner radius: `rounded-lg` (8px) — was 5px, off-grid; snapped 2026-09-16
  as a judgment call (exact tie between `rounded`/4px and `rounded-md`/6px),
  for consistency with Card's container radius, not a data-forced snap.
- Icon (play badge): `w-20 h-14` (80×56px) — was 78×53.625px, off-grid.
  Resized directly (single simple `VECTOR`, intentionally rectangular, low
  distortion risk) rather than wrapped in a container.
→ `0008`

### Chip
`text-sm py-0.5 px-3 leading-tight` — one spec for every chip, regardless of
family (CTA pill with icon, or status pill with a colored dot). Not
`leading-none` — descenders (`g`/`y`/`p`/`q`/`j`) crowd the border at exactly
1.0× line-height.
→ `0008`

Re-audited 2026-09-16 against the standalone Chip reusable component
(`2982:43`): already 100% compliant, no fixes needed. A separate, unrelated
real chip/badge component was found elsewhere in the file (`Component 1`
set, `Components` page) that is NOT yet on this spec (12px text, fixed-px
line-height, NHG font) — flagged, not fixed; out of scope for the
reusable-components work.

**Colored status chips** (wide band's "Certified"/"In Progress" pills,
both breakpoints) — same size spec as above, already compliant. Colors
fixed 2026-09-17 (raw pre-`0001` values → v3 Tailwind hex), then corrected
again 2026-09-18 to real v4 OKLCH-derived values: `bg-green-100`
(`#DCFCE7`, unchanged) / dot `green-600` (`#00A63E`) / text `text-green-800`
(`#016630`) for Certified; text `text-amber-800` (`#973C00`) for In
Progress (background/dot colors have no decided token yet, left as-is).
→ `0001`, `0008`

### Band (section wrapper)
- Container: `max-w-screen-2xl mx-auto px-8` (`lg`+) / `px-4` (mobile)
- Vertical padding: `py-24` (homepage default, `lg`/2xl) / `py-16` (interior
  tier) / `py-12` (mobile)
- Background: `bg-white` (White option) or `bg-[#F4F7FC]` (Slate Gray option,
  arbitrary value — no token exists)
→ `0007`, `0008`

### Footer
- Same container/gutter rules as any band (see above)
- Nav label: `text-base`
→ `0008`

---

## Related

- `decisions/0001` through `decisions/0015` — the ADRs this table is compiled
  from. Every row's `Source` column links back to the decision that reasoned
  it out; read the ADR before changing a value here.
- `decisions/0015` specifically — the reasoning for why the Breakpoint
  Transform Table above exists as one generation-ready lookup rather than a
  second hand-built master per template.
- Figma: "Design Rules — Tailwind Aligned" page (node `2806:3`) — the
  designer-facing version of this same information.
- `decisions/README.md` — the ADR-vs-result architecture this file follows:
  ADRs are canonical reasoning, this file (like the Figma page) is a living
  snapshot of the current result and should be corrected to match an ADR if
  the two ever drift apart.
- `context/audit-procedure.md` — the process-facing counterpart to this
  file: this file says *which* value, that file says *how* to find,
  verify, and fix it, in what order.
