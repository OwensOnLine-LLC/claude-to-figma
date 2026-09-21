# 0012. Every breakpoint mockup gets a full-chain text sizing/alignment audit before sign-off

Status: Accepted (extended by `0017` — a 4th check, orphan/singleton last
lines, re-run after any change that can alter a text block's wrap, not only
at initial copy-authoring time)
Date: 2026-09-10

## Context

Building the base/mobile (375px) breakpoint surfaced the same bug three separate
times, in two different bands, before it was fully caught:

- Narrow band: 4 stat cards, each pairing a short title with a longer
  description. The description was correctly `FILL`-width (so it wraps to the
  column). The title sat inside a wrapper frame that stayed `HUG`, so it kept
  whatever narrow width it last computed — one level deep for two cards, but a
  card in the wide band ("ISO 42001") needed **two** separate `HUG` frames
  fixed in the same chain before the title actually reached the shared column
  width.
- The same pattern recurred independently in the wide band's 4 certification
  cards, which weren't caught in the first pass because their title and
  description happened to already share the same (centered) alignment, masking
  the sizing bug — the titles were still artificially narrow, just not
  visibly *misaligned* against their description the way the narrow band's
  were.
- Separately, three pairs of role-matched text (Hero stat number/label, the
  wide band's eyebrow/headline, and every narrow-band card's title/description)
  had mismatched `textAlignHorizontal` — confirmed present in the real,
  unmodified homepage source too, just far less visible at desktop card widths
  than at a 287px mobile column.
- A fourth, distinct failure mode surfaced later in the same narrow band: the
  gap between each card's description and its chip button was inconsistent —
  generous on card 1, flush with no gap at all on cards 2–4. The reported
  `itemSpacing` value on the containing frame was not the cause (two of the
  four broken cards actually reported the *same* `itemSpacing` as the working
  card). The real cause was `primaryAxisAlignItems`: card 1 used `MIN` (which
  honors `itemSpacing`), cards 2–4 used `SPACE_BETWEEN` — which, combined with
  `primaryAxisSizingMode: 'AUTO'` (a hugging frame has no leftover space to
  distribute), silently collapses the gap to 0 regardless of what
  `itemSpacing` is set to. This is present in the real, unmodified homepage
  source too, not something introduced while building the mockup.

Fixing this by reacting to a screenshot each time is unreliable — the sizing
bug in particular is often invisible in the render when the paired text
happens to still fit inside its stale width, so "it looks fine" is not
sufficient evidence that the chain is correct.

## Decision

Every time a breakpoint mockup (base/mobile, `sm`, `md`, `lg`, `xl` — any of
them) is built or edited, run a full-chain audit before treating it as done,
not just a visual screenshot check:

1. **Alignment pairing, derived from the icon, not from internal agreement.**
   For every text pair presenting as one unit (a title above a description, a
   number above a label, an eyebrow above a headline) inside a card or badge
   that has its own icon, the ground truth for `textAlignHorizontal` is the
   icon's actual position — left-positioned icon means left-aligned text,
   centered icon means centered text. Checking only whether the title and
   description agree *with each other* is not sufficient: the wide band's
   cert cards had internally-consistent centered pairs sitting under a
   left-positioned icon, which is still wrong. Where there's no icon to
   anchor against, fall back to checking agreement between the paired texts
   themselves. A mismatch is a defect even if it renders acceptably at the
   current width — as this file demonstrates, a mismatch that's invisible at
   one width becomes visible at another.
2. **Full-chain sizing.** For every text using `FILL` + `textAutoResize:
   'HEIGHT'` to wrap against a column, find its role-paired sibling and walk
   **its entire ancestor chain** up to the shared column-width ancestor.
   Convert every `HUG` frame found along that path to `FILL`, then set the
   text node's own `layoutSizingHorizontal` to `FILL` as a separate,
   final step — fixing the ancestors does not automatically update the text
   node itself; both were required in every case found here.
3. **Rendered gap, not reported `itemSpacing`.** For every repeated set of
   sibling components (cards, chips, list items), compute the *actual* gap
   between two children — `nextChild.y - (child.y + child.height)` — and
   compare it across all instances in the set. Do not trust `itemSpacing`
   alone: a frame with `primaryAxisAlignItems: 'SPACE_BETWEEN'` and
   `primaryAxisSizingMode: 'AUTO'` reports whatever `itemSpacing` value it was
   last given while actually rendering a 0px gap, since `SPACE_BETWEEN`
   distributes only the *leftover* space in the frame, and a hugging frame
   has none. If instances in the same set disagree, check
   `primaryAxisAlignItems` on each before assuming the spacing values differ.

**Reusable audit script** (run against any band/section root once a
breakpoint's structural edits are believed complete):

```js
function auditPairedText(root, referenceWidth) {
  const texts = root.findAll(n => n.type === 'TEXT' && n.visible);
  const flags = [];
  for (const t of texts) {
    // Flag anything narrower than the reference column that isn't a short,
    // naturally-sized label (buttons/pills/numbers are expected to be HUG).
    if (t.layoutSizingHorizontal === 'HUG' && t.width < referenceWidth * 0.85 && t.characters.length > 20) {
      flags.push({ id: t.id, chars: t.characters.slice(0, 30), issue: 'possible stale HUG width', width: t.width });
    }
  }
  return flags;
}
```

This is a starting heuristic, not a complete linter — it catches the specific
failure mode found here (a long text stuck HUG well under the column width).
Pair it with a manual pass over role-matched text for alignment, since
alignment mismatches aren't detectable from sizing properties alone.

**Second reusable script**, for the rendered-gap check (point 3 above) — run
across a set of repeated sibling components (e.g. every card in a band) and
compare `actualGap` across instances rather than trusting `itemSpacing`:

```js
function auditRenderedGap(cardContentFrames) {
  return cardContentFrames.map(frame => {
    const [a, b] = frame.children; // assumes exactly two stacked children
    return {
      id: frame.id,
      itemSpacing: frame.itemSpacing,
      primaryAxisAlignItems: frame.primaryAxisAlignItems,
      actualGap: b.y - (a.y + a.height),
    };
  });
  // Flag any row whose actualGap diverges from the others, regardless of
  // whether itemSpacing matches — itemSpacing matching is not sufficient
  // proof the rendered gap matches.
}
```

**Do not stop at the immediate parent.** The narrow-band and wide-band cases
both needed a walk of 1–2 intermediate frames before reaching the ancestor
that was already correctly `FILL`. Always walk until reaching a frame whose
width already matches the target column, not just one hop up.

## Alternatives considered

**Fix issues reactively as they're spotted in screenshots, without a
standing audit step.** Rejected — this is what happened for the first two
rounds of this exact bug, and it took three separate discoveries (narrow band,
then a user report that it wasn't fixed everywhere, then the wide band caught
by proactive re-audit) to actually close it out. A visual screenshot check
alone under-detects this class of bug because a stale-but-adequate width often
still renders acceptably.

**Build a full automated Figma lint/plugin for this.** Deferred, not
rejected — worth it if this project keeps producing new breakpoints or
sections at the current pace, but the heuristic script above is enough for
now; a full linter is more investment than the current scope justifies.

## Consequences

- Every future breakpoint mockup (and any edit to an existing one) gets this
  audit before being reported as done, alongside the existing screenshot
  verification.
- All 3 known instances of the sizing bug (narrow band ×4 cards, wide band ×4
  cert cards), all 3 known instances of the alignment mismatch (Hero
  stat labels, wide-band eyebrow, narrow-band card titles), and the
  `SPACE_BETWEEN`-masks-`itemSpacing` gap bug (narrow band ×4 chip/description
  pairs) are fixed on the base/mobile mockup as of this ADR.
- Point 1 above was corrected mid-flight because of this: an earlier pass over
  the wide band's cert cards checked only title-vs-description agreement,
  missed the icon mismatch, and reported them as fine. "Internally
  consistent" is not the same check as "matches the icon."
- `lg` was re-audited against this rule on 2026-09-14, confirming the value
  of running it at every breakpoint rather than assuming a fix caught at one
  width propagates: the icon-alignment fix (point 1) had never been applied
  to `lg`'s cert cards (still centered/`HUG`) despite being fixed on mobile,
  and the footer's off-grid values (84/15/50) turned out to be pre-existing
  source bugs present on both breakpoints, not mobile-only drift. Also found:
  the mobile-only 16/24/16 card-gap alternation in the wide band does not
  apply to `lg`, since `lg`'s cert-card grid is a genuine 2-column layout,
  not a stacked one — this distinction is now generalized as a standing rule
  in `decisions/0015`'s Breakpoint Transform Table rather than left as a
  one-off finding. The 2xl baseline remains un-audited.
- If a future skill is built to generate responsive sections or breakpoints
  (an idea already flagged as open in `decisions/0008`), this audit should be
  a mandatory step in that skill's verification phase, not an optional
  follow-up.

## Related

- `decisions/0008-tailwind-aligned-layout-and-type-rules.md`,
  `decisions/0009-responsive-type-scale.md` — the container/type rules this
  audit protects once applied to a real breakpoint mockup; `0008`'s mobile
  vertical-padding tier was itself verified using this ADR's "check the
  rendered result" discipline.
- `decisions/0010` (content parity) and `decisions/0011` (authoring
  direction) — the content-side rules that share the same "verify the whole
  chain, not just what's visible" discipline.
- Figma: "Owens OnLine Homepage — base/mobile (375px)" frame (`2834:47722`) on
  Marketing Pages R&D — where all currently-known instances were found and
  fixed.
