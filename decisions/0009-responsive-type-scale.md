# 0009. Large headline roles get a responsive type scale (size, line-height, tracking); small text stays fixed

Status: Proposed
Date: 2026-09-10

## Context

`decisions/0008` aligned every measured font size to an exact Tailwind token, but
only checked the sizes shipped at one width (the real, desktop-width homepage).
It never addressed whether a size should *change* across breakpoints — Tailwind
utility classes (`text-5xl`, etc.) are static by default and only vary by
viewport when a responsive variant (`lg:text-5xl`) is explicitly added.

Building the `lg` (1024px) and base/mobile (375px) breakpoint versions of the
homepage sections (see the two "Owens OnLine Homepage — ..." frames on Marketing
Pages R&D) surfaced the consequence directly: the Hero headline and the two
section headlines ("Not an algorithm. A network of people.", "Your data,
protected.") kept their full 48px (`text-5xl`) size at every width. At `lg` this
still read fine — the stacked column is wide enough. At base/mobile (343px
content width) it wrapped across 5 lines, taking up a disproportionate share of
the viewport before any body copy was visible.

A second, related problem surfaced only after the font-size fix landed: shrinking
these same three headlines from 48px to 30px changed `fontSize` but left
`lineHeight` untouched at its old *fixed pixel* value (48px, or 52px for the
narrow-band headline) inherited from the 48px version. A fixed-pixel line-height
doesn't scale with font size the way a percentage one does, so the relative
leading nearly doubled — 1.6–1.73× the new font size, versus the 1.0× ("tight")
leading the real desktop headlines actually use. The visible symptom was
identical in kind to the first problem: wasted vertical space on a viewport that
has none to spare, just from a different property.

## Decision — Part 1: font size

Only the large display/heading roles get a responsive step-down for narrower
viewports. Small text (body copy, eyebrows, buttons, nav, card titles, footer
text) stays at a single fixed size across all breakpoints — this matches
standard Tailwind practice: text in the 14–24px range reads fine at any width
and doesn't need scaling, while a 48–60px headline visibly overwhelms a narrow
column if left unscaled.

| Role | Base/mobile | lg | 2xl (unchanged, per `decisions/0008`) |
|---|---|---|---|
| Hero headline | `text-3xl` (30px) | `text-5xl` (48px) | `text-5xl` (48px) |
| Section / CTA headline | `text-3xl` (30px) | `text-5xl` (48px) | `text-5xl` (48px) |
| Interior page H1 | `text-4xl` (36px) | `text-6xl` (60px) | `text-6xl` (60px) |

Not scaled — fixed at their `decisions/0008` size across every breakpoint:
Hero/Section eyebrow, Body/Subheadline, Card/feature title, Card description,
Button label, Footer heading, Footer nav label.

Every value above is an exact Tailwind default step (`text-3xl`=30px,
`text-4xl`=36px, `text-5xl`=48px, `text-6xl`=60px) — same "snap to a real token,
never an arbitrary value" rule `decisions/0008` established. `lg` keeps the
`2xl` size unchanged for the two headline roles because it already reads
correctly at that width (confirmed visually) — this only steps down at
base/mobile, where it didn't.

## Decision — Part 2: line-height

Wherever a headline's `fontSize` is set per-breakpoint (the table above),
`lineHeight` must be set as a **percentage of the font size, never a fixed
pixel value** — a pixel value silently stops scaling the moment a future edit
changes the font size again, which is exactly what happened here. Percentage
line-height is Tailwind's own model: every `leading-*` utility (`leading-none`,
`leading-tight`, `leading-snug`, ...) is a unitless ratio in CSS, which is a
percentage of whatever font size is currently applied, not an absolute value.

| Role | Base/mobile line-height | lg / 2xl line-height |
|---|---|---|
| Hero headline | 125% (`leading-tight`) | 100% (`leading-none`, confirmed on the real homepage) |
| Section / CTA headline | 125% (`leading-tight`) | 100% (`leading-none`, confirmed on the real homepage) |
| Interior page H1 | 125% (`leading-tight`, recommended — no interior mockup built yet to confirm against) | 100% (`leading-none`, assumed to match the other two headline roles; not yet verified against a real instance) |

Mobile uses a looser ratio than desktop deliberately, not as an oversight: these
headlines wrap to 3–5 lines on a 343px column versus 1–2 lines at `lg`/`2xl`,
and true `leading-none` (1.0×) risks feeling cramped over that many lines.
`leading-tight` (1.25×) recovers nearly all the wasted space the fixed-pixel bug
caused while staying safely readable at that line count.

## Decision — Part 3: letter-spacing

The same three headline roles get `tracking-tight` (-0.025em / -2.5%) at
**every** breakpoint — 2xl, `lg`, and base/mobile alike. Large bold display
text has more optical space between letters than body text does, and a small
negative tracking value tightens that back up; this is standard practice
specifically for large headings (short, bold), not for body copy or small
text, where negative tracking measurably hurts readability and runs against
the spirit of WCAG SC 1.4.12 (text spacing). Never apply this outside the
headline roles.

Unlike line-height, letter-spacing does **not** need a different value per
breakpoint: the reason line-height needed two tiers was line *count*
(3–5 lines at mobile vs. 1–2 at `lg`/`2xl`), and tracking has no equivalent
dependency — one ratio holds at every width. Like line-height, it must be set
as a relative unit (Tailwind's `tracking-*` scale is `em`-based, i.e.
percentage-of-font-size), never a fixed value, for the same reason: a fixed
value stops scaling the moment font size changes again.

`-2.5%` (`tracking-tight`) was chosen over an initial manual test of `-2%`
because it's an exact Tailwind token — same "snap to a real token, never an
arbitrary value" rule as everywhere else in this ADR and `decisions/0008`. The
difference between -2% and -2.5% is not visually meaningful at these sizes.

## Alternatives considered

**Fluid typography via `clamp()` (a size that scales continuously with
viewport width, not just at breakpoints).** Rejected — it's a legitimate
Tailwind technique (`text-[clamp(1.875rem,5vw,3rem)]`) but breaks from the
"exact named token" discipline this project has followed since `decisions/0001`;
a clamp value isn't a token at all, arbitrary or otherwise. Worth reconsidering
later if the two-step scale here proves too coarse, but not the default.

**Scale every text role, not just headlines.** Rejected — this is not standard
practice and there was no visual evidence of a problem at small sizes; card
titles (24px) and body copy (18px) rendered correctly at 375px in the mobile
mockup without any change. Scaling roles that don't need it adds inconsistency
(now every role needs a breakpoint decision) for no visual benefit.

**Add an `sm`/`md`/`xl` step to the scale table now.** Rejected for this pass —
scope matches `decisions/0008`'s mockups, which only cover base/mobile and `lg`.
If `sm`, `md`, or `xl` mockups are built later, they need their own values
added to this table, most likely `text-4xl` (36px) as a midpoint between the
30px base and 48px `lg`/`2xl` value for the two headline roles.

**Match desktop's exact 1.0× (`leading-none`) ratio at mobile too, instead of
1.25×.** Rejected — would recover slightly more space, but desktop's headlines
only run 1–2 lines while mobile's run 3–5; a ratio tight enough to look
intentional over 1–2 lines can read as cramped over 3–5. `leading-tight` was
chosen as the ratio that recovers nearly all the wasted space from the
fixed-pixel bug without introducing a new readability problem in its place.

**Extend negative tracking to body copy / smaller text, since it "looked
better" on the headline.** Rejected — this is precisely the mistake industry
guidance warns against. Negative tracking is a large-display-type technique;
applying it below that size range trades readability for a marginal visual
tightening with no offsetting benefit, and cuts against WCAG 1.4.12's
concern for text-spacing accommodation. Scope stays headline-only.

## Consequences

- The Hero headline and the two section headlines in the base/mobile mockup
  needed their `fontSize` changed from 48 to 30 to match this table — done as
  part of landing this ADR, on the mobile clones only (the real homepage and
  the two `/create-band` master templates are untouched).
- The same three headlines also needed their `lineHeight` changed from a fixed
  48px/52px to a 125% (`leading-tight`) ratio, applied after the font-size fix
  had already landed and the excess space was noticed independently. `lg`
  needed no line-height change — its headlines never had their font size
  touched, so the original fixed-pixel value still happens to equal the
  correct 100% ratio there.
- `lg` needed no font-size changes either — it was already at the correct
  size per this table.
- Any future breakpoint mockup (sm, md, xl, or a real interior-page H1 example)
  must set both headline font size *and* line-height (as a percentage) from
  this table, rather than carrying over whatever values the previous
  breakpoint used — carrying over an unscaled pixel line-height is exactly
  how this ADR's second bug happened.
- All 6 headline instances across the mobile and `lg` mockups (Hero, Section,
  and CTA headline roles at both breakpoints) now carry `tracking-tight`
  (-2.5%) consistently. Two of the six (the mobile narrow-band and wide-band
  headlines) already had a manually-tested -2% value applied before this ADR
  section existed; all six are now standardized on the exact Tailwind token
  instead. `lg` and 2xl previously had no tracking applied at all — this is a
  net-new value for those, not a correction of an existing one.
- This table is the second piece of the responsive spec, alongside
  `decisions/0008`'s container/spacing rules — together they're what a future
  freeform "create-section" flow (referenced as an open idea in `decisions/0008`)
  would need to consult for both breakpoint-independent and breakpoint-dependent
  values.

## Related

- `decisions/0008-tailwind-aligned-layout-and-type-rules.md` — the base type
  scale (one size per role, no breakpoint variation) this extends.
- `decisions/0012-responsive-text-container-audit.md` — the line-height bug is
  the same category of mistake that ADR's audit rules are built to catch:
  a property that reads as plausible in isolation (a 48px line-height is
  perfectly normal — just not paired with a 30px font) but is wrong once
  checked against its actual paired value rather than trusted on its own.
- Figma: "Design Rules — Tailwind Aligned" page, new "Responsive Type Scale"
  section (node `2806:3`) — documents this table for review.
- Figma: "Owens OnLine Homepage — lg (1024px)" (`2822:47699`) and "— base/mobile
  (375px)" (`2834:47722`) frames on Marketing Pages R&D — where this rule is
  applied.
