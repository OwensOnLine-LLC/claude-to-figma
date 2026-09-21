# 0010. Mobile may condense or collapse content, but never hide it

Status: Proposed
Date: 2026-09-10

## Context

`decisions/0008` and `decisions/0009` made the `lg` (1024px) and base/mobile
(375px) breakpoint mockups reflow correctly — every desktop container, spacing
value, and font size has a defined mobile equivalent. Neither decision touched
*content*: both mockups carry over 100% of the real homepage's copy, cards, and
stats at every breakpoint, just re-stacked (a 4-across stat row becomes 2×2;
a 2×2 cert-card grid becomes a single column; a 4-column footer nav becomes one
stacked column). Nothing was shortened or removed.

This is safe, but it has a visible cost: the mobile page is very long. The
narrow content band alone runs roughly 2400px tall at 375px width, versus a
fraction of that on desktop, because stacking trades horizontal space for
vertical space with no compression anywhere. The question this raises — should
mobile ever show *less* than desktop, and if so, how — already has a real
answer in this project, just not in the part of the doctrine that governs
building new designs.

`CLAUDE-MOBILE.md` (the `/commands` audit skill's Type 2B addendum) is written
to *audit* shipped pages for mobile content parity, for SEO/GEO reasons: Google
indexes the mobile DOM as the primary document, so content invisible on mobile
is invisible to search and AI crawlers. Its own risk classification already
draws the exact line this ADR needs:

| Adaptation | Risk |
|---|---|
| Reflow — same content, narrower column | NONE |
| Condensed string — shorter text, same keywords | LOW, if keywords retained |
| Hidden via `display:none` | HIGH — not indexed |
| JS-conditional — renders only above a breakpoint | HIGH — not indexed |
| Removed from mobile entirely | HIGH — not indexed |

That doctrine has never been connected to the `/create-band` + Tailwind
breakpoint build track — nothing told a future build "here's what you're
allowed to shorten and what you must never cut."

## Decision

Content parity governs every future responsive build the same way it governs
audits: **parity means the substance stays crawlable and present, not that the
visual presentation stays identical at every width.**

1. **Never hidden — for indexable content.** No text content block is ever
   removed from a mobile design, put behind `display:none`, or rendered only
   above a breakpoint. This is an absolute rule, not a judgment call. It does
   **not** extend to purely decorative visual assets (photography, background
   illustration) that carry no trigger keyword or entity-attribute-context of
   their own — `CLAUDE-MOBILE.md`'s parity concern is specifically about text
   Google and AI crawlers index, and a decorative image has no text signal to
   lose. Hiding a decorative image at a breakpoint (Tailwind's `hidden
   lg:block` pattern) is a layout decision, not a parity violation, as long as
   the actual claim it illustrates stays present as text elsewhere (it
   usually already is, per rule 4 below).
2. **Condensing is allowed and often preferred.** Mobile copy may be shorter
   than desktop copy as long as the trigger keywords and the
   entity–attribute–context of the claim survive (per `CLAUDE-MOBILE.md`
   Criterion E: cut the preamble, keep the signal). A shorter string that
   keeps the same keywords is not a parity violation — `CLAUDE-MOBILE.md`
   scores this as **Superior Density**, not a defect.
3. **Collapsing is allowed for secondary detail.** An accordion/expander that
   keeps content in the DOM but not always visually expanded is an accepted
   pattern for reducing mobile scroll length (`CLAUDE-MOBILE.md` already
   permits this for FAQ answers). A carousel/scroller in place of a long
   vertical card stack is the equivalent pattern for a grid.
4. **Never condensed or collapsed, at any breakpoint:** the Master Criterion
   phrase (human/in-country investigator language), the primary CTA, hero
   headline and subhead trigger keywords, compliance framework names in body
   text (not badge-only), and quantified social proof numbers. This is the
   same list as `CLAUDE-MOBILE.md`'s "Must-Check Items."
5. **Everything else is a candidate, not a default.** A long stat block, an
   extended cert-badge grid, secondary explanatory paragraphs — these may be
   condensed or collapsed specifically because doing so shortens mobile scroll
   without violating parity, but *which* sections get that treatment is a
   content/UX call for a person to make, flagged explicitly, never applied
   silently as a side effect of a layout pass.

## Alternatives considered

**Keep every mobile design as full reflow, always fully expanded (the current
default in the two existing mockups).** Rejected as the permanent answer, kept
as the safe fallback. It carries zero parity risk, which is why it's what
`decisions/0008`/`0009` produced by default — but it doesn't solve the actual
problem raised here, and treating "reflow everything, cut nothing" as the final
state leaves every content-heavy section at full, disproportionate mobile
length forever.

**Let each future build decide ad hoc whether to shorten or hide content for
mobile.** Rejected — this is precisely the failure mode `CLAUDE-MOBILE.md` was
written to catch after it happened for real (see that file's derivation from
actual 2026-04 parity audits that found lost content). An explicit, shared rule
prevents re-litigating "is this safe to cut" section by section.

## Consequences

- The two existing breakpoint mockups (`lg` 1024px, base/mobile 375px on
  Marketing Pages R&D) need no immediate fix — full reflow with zero content
  removal is already fully compliant with this ADR, it's just the least
  compressed point on the spectrum this ADR opens up.
- First applied example of rule 1's decorative-image carve-out (added
  2026-09-11): the Hero's portrait photo is hidden on base/mobile
  (`visible: false` in the mockup, `hidden lg:block` in Tailwind), kept on
  `lg`/2xl. Dropped the mobile Hero from 1114px to 758px (356px, ~32%
  shorter). No parity loss — the trust claim the photo illustrates is carried
  by text already on rule 4's never-cut list ("trained locally based
  investigators," Human driven verification), which stays present on mobile
  either way. The Hero's own blurred background fill (a separate layer from
  the portrait) still renders, so mobile isn't left with a flat empty gap
  where the photo was.
- Future responsive builds should actively look for condense/collapse
  candidates using the priority list in point 4/5 above, rather than defaulting
  to "reflow only."
- This ADR does not write any condensed copy itself — that's a follow-up
  content task once specific candidate sections are chosen for review, the same
  way `decisions/0008` flagged type-role conflicts without picking winners.
- This is the first ADR to explicitly bridge the `/commands` audit doctrine
  (`CLAUDE-MOBILE.md`) and the `/create-band` + Tailwind-breakpoint build
  track. Future changes to either side should check the other for drift, per
  `decisions/README.md`'s policy — if `CLAUDE-MOBILE.md`'s parity rules change,
  this ADR's rule list needs re-checking, and vice versa.

## Related

- `CLAUDE-MOBILE.md` — the existing SEO/GEO content-parity audit framework this
  ADR's rules are drawn from directly (Criterion E, Must-Check Items,
  Redundancy Mapping Rule, the risk classification table reproduced above).
- `decisions/0008-tailwind-aligned-layout-and-type-rules.md`,
  `decisions/0009-responsive-type-scale.md` — the layout/spacing and type-scale
  rules this complements; those govern how content reflows and resizes, this
  governs whether and how much of it should condense.
- Figma: the `lg` (1024px, node `2822:47699`) and base/mobile (375px, node
  `2834:47722`) frames on Marketing Pages R&D — first candidates for applying
  condense/collapse once specific sections are chosen.
