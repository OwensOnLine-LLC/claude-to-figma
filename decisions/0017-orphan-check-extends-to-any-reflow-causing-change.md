# 0017. The full-chain audit gets a 4th check: orphan/singleton last lines, re-run after ANY reflow-causing change

Status: Accepted
Date: 2026-09-14

## Context

Widening the custom test band's headline column (to close the gap left when
its row was made to reach the full 1536px container, then again when the
column-to-column gap changed from 24 to 48) reflowed its subhead text to a
new line count. The new wrap left a single word ("aliqua.") alone on the
last line — an orphan. Nothing caught it.

The no-singleton/orphan rule already exists in this project — it's why the
Hero headline's manual line break was removed earlier (a stray "and" was
left alone on its own line once the font size shrank for mobile). But that
fix happened because someone was looking directly at that headline while
editing its copy. The rule was never written into `decisions/0012`'s audit
checklist, which is the thing that actually gets re-run after a *structural*
change. `0012` already covers three failure modes surfaced by resizing and
reflowing (stale-width sizing, alignment mismatches, rendered-gap-vs-
itemSpacing) — orphans are a fourth failure mode in exactly the same
category (a property that looks fine until the actual rendered text is
checked at the new size) that was simply never added to the list.

The result: two structural changes in a row (widen for container-width
compliance, widen the gap) each reflowed the same text block, and neither
one triggered an orphan check, because "resize a container" and "check for
orphans" had never been linked as one procedure. The rule existed; it just
weakly wasn't attached to the events that actually cause it.

## Decision

**Add a 4th point to `decisions/0012`'s full-chain audit:** any change that
can alter a wrapped text block's line count — a container resize, an
`itemSpacing`/gap change, a font-size change, a breakpoint switch, or a copy
edit — is followed by checking every text block whose width or font
actually changed for a single short word (a true orphan) or very short
final line, not just at initial copy-authoring time. This generalizes the
existing informal rule (previously triggered only when copy was being
written or edited) to cover the structural side `0012` already owns.

**Preferred fix: a non-breaking space (` `) between the last two
words**, so they wrap together onto the final line instead of splitting —
this keeps the copy completely unchanged, just prevents the break at that
one point. Confirmed 2026-09-14 on the custom test band's subhead: the
orphan ("aliqua." alone) was first fixed by trimming the sentence, but the
preferred outcome is preserving full copy with `magna aliqua.` so the
last line reads "magna aliqua." — two words, no content lost. Trimming or
lightly rephrasing stays the fallback only when pairing the last two words
still leaves a line too long or otherwise looks wrong. Never fix an orphan
by reintroducing a manual line break — a manual break is exactly what
caused the *other* known orphan bug in this project (the Hero headline
orphaning "and" once its font size shrank for mobile,
`decisions/0012`/`0009`'s history). All three directions of the same
failure — a manual break becoming wrong at a new size, a natural wrap
becoming wrong at a new width, and the fix itself needing to preserve
content — are covered by the same rule: re-check after the change, join the
last two words with a non-breaking space first, trim only if that's not
enough.

## Detection mechanism (added 2026-09-14, second incident)

A whole-band screenshot glance is not reliable enough on its own — confirmed
by a second orphan (the B2B brief band's "Centralized Customer Support"
card, "Chat." alone on line 3) slipping through a screenshot review that
was looking directly at that card. Figma's plugin API exposes no per-line
text layout (no call returns "here are the wrapped lines"), so this can't
be fully automated the way `0012`'s sizing/gap checks can. The reliable
procedure is two steps, not one:

1. **Flag candidates with a heuristic script**, using `height ÷ lineHeight`
   to estimate line count for every text node touched by the current
   content pass — this part is fully reliable, both are real properties:
   ```js
   function flagMultilineTextForOrphanCheck(root) {
     const texts = root.findAll(n => n.type === 'TEXT' && n.visible);
     return texts.map(t => {
       const seg = t.getStyledTextSegments(['fontSize', 'lineHeight'])[0];
       const lh = seg.lineHeight;
       const lineHeightPx = lh.unit === 'PERCENT' ? seg.fontSize * (lh.value / 100)
         : lh.unit === 'PIXELS' ? lh.value : seg.fontSize * 1.2;
       return { id: t.id, chars: t.characters.slice(0, 40), estimatedLines: Math.round(t.height / lineHeightPx) };
     }).filter(r => r.estimatedLines >= 2);
   }
   ```
2. **Every flagged node gets its own explicit visual read**, not a glance at
   the whole band — zoom enough to actually state what the last line says
   and how many words are on it, for each flagged node individually, before
   the content pass is reported done. A single full-band screenshot is not
   a substitute for this per-node check, even when that card is visibly
   included in it.

## Alternatives considered

**Only check for orphans when copy is being written or edited (status
quo).** Rejected — this is precisely what just failed. A structural change
having nothing to do with copy (a gap value, a container width) reflowed
text and produced exactly the defect this rule exists to prevent.

**Add an automated line-count/last-line-word-count heuristic script, similar
to `0012`'s `auditPairedText`.** Deferred, not rejected — a script could flag
"last line has 1 word," but distinguishing a genuine orphan from a
deliberately short final line (e.g. a one-word sentence) still needs human
judgment; not worth building until this manual check proves insufficient on
its own, same reasoning `0012` used for deferring a full linter.

## Consequences

- `decisions/0012`'s audit is now 4 points, not 3 — any future reference to
  "run the full-chain audit" includes this check.
- The custom test band's headline subhead (fixed as part of landing this
  ADR) is the first applied instance.
- This applies to placeholder/lorem-ipsum text as much as real copy — the
  defect is typographic, not content-dependent. Once real copy replaces a
  placeholder, it still needs its own orphan check; fixing the placeholder
  now doesn't exempt the real copy later.
- Every resize done earlier in this session (the row's width fix, both gap
  changes) is now retroactively covered — this was the one that happened to
  produce a visible orphan, but any future resize of already-populated real
  copy needs the same check, not just this one.

## Related

- `decisions/0012-responsive-text-container-audit.md` — the audit this adds
  a 4th point to; same "verify the actual rendered result, not the property
  in isolation" discipline, extended from sizing/alignment/gap to line
  wrapping.
- `decisions/0009-responsive-type-scale.md` — where the original Hero-
  headline orphan (the manual-line-break direction of this same bug class)
  was first found and fixed.
- Figma: custom test band `2958:161` on Band Drafts — where this was found
  and fixed.
