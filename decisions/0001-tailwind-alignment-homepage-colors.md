# 0001. Tailwind alignment for the homepage color system

Status: Proposed
Date: 2026-09-06

## Context

The homepage color system was extracted from the live Figma frame (after excluding
image and third-party content — see the forthcoming ADR on that policy) and came
back with 20 values. Several were near-duplicates of each other, and several sat
close enough to a Tailwind default-palette stop that hand-picked hex values were
adding maintenance cost without adding any visual distinction — dev has to
reproduce `#3F4C5D` exactly forever instead of writing `slate-600`.

The obvious move — snap every neutral onto the nearest Tailwind stop — runs into a
real conflict: `Border Strong (#64748B)` is an *exact* match to `slate-500` (ΔE 0.0),
which argues for standardizing the whole neutral ramp on **slate**. But `Ink
(#2A3133)` — the single highest-usage value in the system at 155 instances, carrying
every heading and body copy — is a poor fit for slate (ΔE 11.8) and only a
middling fit for its best alternative, zinc-800 (ΔE 5.2). No single Tailwind gray
family fits both the borders and the primary text color well.

## Decision

Standardize the neutral ramp on **slate**, because `Border Strong` was already an
exact match and slate's blue undertone matches the brand's other blue-leaning
neutrals (`Border Blue`, `Mist`, `Navy Gray` all skew blue in their raw hex).

Keep **Ink exact, unchanged**, as the one deliberate exception. The nearest
Tailwind match is a visible warm-to-cool shift on the site's primary reading
color, and the value is used too often to change as a side effect of a token
cleanup rather than a deliberate rebrand call.

Adopt exact Tailwind stops everywhere else the fit was safe (ΔE < 5, then
re-verified against WCAG 2.2 before adoption — never snapped on hex distance
alone):

| Was | Now | Token |
|---|---|---|
| Action Blue `#286BE0` | `#2563EB` | `blue-600` |
| Link Blue `#1C4FBF` | `#1D4ED8` | `blue-700` |
| Fog `#F5F5F7` | `#F1F5F9` | `slate-100` |
| Pale Blue `#EDF2FE` | `#EFF6FF` | `blue-50` |
| Slate `#3F4C5D` | `#475569` (v3) / `#45556C` (v4 OKLCH, see below) | `slate-600` |
| Navy Gray `#273444` | `#334155` | `slate-700` |
| Border Strong `#64748B` | unchanged | `slate-500` |
| Verify Green `#22A55B` | `#16A34A` | `green-600` |
| Green Tint `#D1F0D5` | `#DCFCE7` | `green-100` |
| Green Ink `#0E5A1C` | `#166534` | `green-800` |
| Gold Ink `#856210` | `#92400E` | `amber-800` |

Merge three near-duplicate pairs that were only accidentally distinct:

- **Brand Navy + Band Navy** — 1.06:1 contrast between them; perceptually the
  same darkness. One token now covers the logo and dark conversion bands.
- **Border Blue + Mist** — both landed nearest `slate-300`, and the two roles
  (light-mode divider, dark-mode muted text) never appear on the same ground.
- **Cream + Cream Glow** — two single-use, off-scale yellows with no shared
  home. Unified into one `amber-100`, which also now backs the Gold Ink badge
  pairing.

Net: 20 values → 17. 13 sit on exact Tailwind stops; Brand Navy, Footer Navy, and
Ink remain custom by name, not by oversight.

## Addendum: Tailwind v4 uses OKLCH, not the v3 hex palette (2026-09-17/18)

Every resolved value in the table above was originally applied using
Tailwind v3's fixed hex palette. Tailwind v4's real default palette is
defined in OKLCH (`oklch(L% C H)`) as its source of truth — the hex codes
commonly cited for v4 classes are just one possible sRGB rendering of that
OKLCH value, and for several stops (`blue-500`, `green-600`, `amber-400`,
both reds, in the set this project uses) the true OKLCH color **exceeds
sRGB gamut** and gets clamped, meaning v3's hex and v4's real color are
genuinely different, more than a rounding difference.

Figma's Plugin API has no native OKLCH input — fills are RGB (0–1) only.
The correct procedure, used throughout this project from 2026-09-18
onward: take the real `oklch(L% C H)` value from Tailwind's docs, convert
precisely via OKLCH→OKLab→linear-sRGB→gamma-encoded-sRGB (Björn Ottosson's
standard OKLab reference matrices — fixed constants, never eyeballed),
clamp to `[0,1]`, and flag any color where clamping actually changed a
value (that's the signal a color is out-of-gamut, not a bug). This is not
an approximation of the real color — it's the exact sRGB rendering of it,
identical to what any non-P3 screen (the large majority) would show
regardless of tool.

Every color this project has touched, re-verified against the real v4
values and corrected 2026-09-18 (see `0008` for the per-component detail):

| Role | v3 hex (originally applied) | Real v4 (OKLCH-derived) | Gamut |
|---|---|---|---|
| blue-500 (Get Demo Hover) | `#3B82F6` | `#2B7FFF` | clamped |
| blue-600 (Get Demo Default, Chip) | `#2563EB` | `#155DFC` | in-gamut |
| blue-700 (Get Demo Down / Link Blue) | `#1D4ED8` | `#1447E6` | in-gamut |
| slate-500 (Cancel Hover) | `#64748B` | `#62748E` | in-gamut |
| slate-600 (Cancel Default) | `#475569` | `#45556C` | in-gamut |
| slate-700 (Cancel Down) | `#334155` | `#314158` | in-gamut |
| green-100 (Certified bg) | `#DCFCE7` | `#DCFCE7` | identical |
| green-600 (Certified dot) | `#16A34A` | `#00A63E` | clamped |
| green-800 (Certified text) | `#166534` | `#016630` | in-gamut |
| amber-800 (In Progress text) | `#92400E` | `#973C00` | clamped |

Find Products' red (`#D82729`) is deliberately **excluded** from this
migration — it's a brand-mandated literal (`CLAUDE.md`'s reserved
`#D8272B`), not a value derived from Tailwind's scale, so there is no "v4
version" of it to move to; doing so would move it away from the brand
mandate, not toward correctness.

A live swatch comparison (v3 vs. real v4, every row above, plus `amber-50`/
`amber-400` for the still-undecided In Progress background/dot) is on the
"Reusable Components" Figma page for visual reference.

## Alternatives considered

**Force every neutral onto one family, including Ink.** Rejected — the best
available family-wide fit (zinc) is still a ΔE 5.2 shift on the highest-usage
color in the system, and "full conformance" isn't worth a visible change to body
text with no user-facing benefit.

**Keep every value custom, adopt no Tailwind stops.** Rejected — `Border Strong`
already *was* `slate-500` exactly before this pass started; refusing to name that
match is refusing a fact already true of the file.

**Match by raw hex distance (Euclidean RGB) instead of Lab ΔE.** Rejected — RGB
distance doesn't track human perceived difference well at low lightness, which is
exactly where several of these merge decisions (Brand Navy/Band Navy, the neutral
ramp) live.

## Consequences

- Border, secondary-text, and icon colors are now expressible as Tailwind
  utility classes directly; only Brand Navy, Footer Navy, and Ink need a
  `tailwind.config` custom key.
- Action Blue → `blue-600` / Link Blue → `blue-700` isn't just a snap — it
  matches Tailwind's own default/hover step convention, which already agreed
  with this project's independent finding that CTA hover must go darker, never
  lighter.
- The one open risk this doesn't resolve: if a future rebrand *does* want to
  move Ink onto the Tailwind scale, that's a visible change to the site's
  primary text color and needs its own sign-off — this ADR is not silent
  permission to do that later without discussion.
- A regression to watch: any future color extraction pass should re-check
  whether `Border Strong` is still an exact `slate-500` match. If a future
  brand refresh changes that value even slightly, the whole "slate, not
  gray/zinc" argument in this ADR should be re-opened, not assumed to still hold.

## Related

- Figma: *Design System — Colors* page, section 09 "Tailwind Alignment"
  (`node-id=2664-2`) — carries the full ΔE table and WCAG re-verification per
  value; this ADR is the canonical statement of *why*, that section should cite
  it rather than restate the reasoning.
- Depends on the color set already having excluded image and third-party
  content — see the forthcoming ADR on that policy.
