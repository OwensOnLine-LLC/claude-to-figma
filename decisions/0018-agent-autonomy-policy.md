# 0018. Autonomy policy — what an agent may fix unilaterally vs. must stop and ask

Status: Accepted
Date: 2026-09-21

## Context

The reusable-components audit work (Button, Card, Chip, Media Frame, colors)
repeatedly hit the same fork: is this finding safe to fix immediately, or
does it need a human decision first? In an interactive session, that fork
was resolved by judgment in the moment, case by case. Moving toward an
actual agent (per the `multi-band-page-agent-goal` memory note's Phase 1)
means that judgment has to become a written rule the agent checks against —
there's no one watching turn-by-turn to catch a wrong guess before it lands
on a real master component.

This project's own history is the evidence base. Looking back at every
finding from the Button/Card/Chip/Media-Frame/color audits, a clean pattern
separates the ones fixed immediately from the ones that stopped for a
question:

**Fixed immediately, no question asked:**
- Card's fixed-pixel→percent line-height (exact match to Tailwind's own
  default ratio for that size — zero visual change, pure mechanism fix).
- The green "Certified" chip colors (exact match to `0001`'s already-
  decided-but-unapplied resolution).
- Font-family migration to the Inter Bold stand-in (an already-standing,
  previously-settled decision — re-asking would be redundant, per that
  decision's own text).
- Get Demo's blue-600/500/700 (exact match to `0001`'s Action Blue/Link
  Blue resolutions, including on a live, already-instanced master
  component — the value being decided already covers the blast radius).
- Button icon snap to `w-3`/`w-2.5` (simple, near-square vector; low
  distortion risk was itself verified, not assumed, before proceeding).

**Stopped and asked:**
- Card's icon container approach (the icon's composite, irregular shape
  meant a naive resize would visibly distort it — a real tradeoff with no
  single correct answer).
- Cancel button's Hover/Down shade formula (no existing decision covered a
  brand-new color; "what does Tailwind do" had a real, non-obvious answer
  that needed surfacing, not assuming).
- Cancel's inherited search icon (semantically wrong for a cancel action,
  but changing it is a content/design call, not a spec-compliance one).
- The amber chip's background/dot colors (genuinely not in any decided
  palette — inventing one would be a new design decision presented as a
  mechanical fix).
- Where the "gray button" actually lived (a factual gap in what's
  documented, not something to guess at).

## Decision

**Fix immediately, without stopping to ask, when the finding is:**

1. **An exact match to an already-decided value elsewhere in the
   doctrine** (`0001`, `0008`, `0009`, `0013`, or any Accepted/Proposed
   ADR) that simply hasn't been applied here yet. The decision was already
   made; applying it is mechanical, not a new judgment.
2. **Covered by an already-standing, previously-settled decision** (e.g.
   the Inter Tight → Inter Bold stand-in). If an ADR says "don't ask about
   this again," don't.
3. **A unit/mechanism fix with zero visual change** — e.g. fixed-pixel to
   percent line-height at the exact same ratio. Confirm the "zero change"
   claim by checking the math, don't assume it.
4. **Extending an already-proven pattern to a missing sibling variant**
   within the same real component set, reusing that pattern's own existing
   values (e.g. Cancel and Find Products's Default state, once the pattern
   and palette were otherwise settled) — not inventing new values along
   the way.
5. **A resize whose distortion risk has been checked and is low** —
   simple/near-square vectors, verified by inspecting the icon's actual
   child structure first (step 7 of `context/audit-procedure.md`), not
   assumed from its type name alone.

Blast radius (a fix touching a real, already-instanced master component)
does **not** by itself require stopping to ask — it requires verifying
propagation afterward (`audit-procedure.md` step 12), not permission
beforehand, as long as the underlying value is already decided per rule 1.

**Stop and ask when the finding is:**

1. **No decided value or token exists for what's needed** — a new color,
   a new shade formula, a new spacing tier. Applying a guess and labeling
   it a "fix" misrepresents a design decision as a compliance correction.
2. **A mechanical fix carries real, non-negligible distortion or content
   risk** — resizing a composite/irregular icon, rewording real copy,
   removing an orphan by trimming rather than joining words.
3. **The fix touches semantic or content correctness**, not just spec
   alignment — an icon that's the wrong choice for its context, wording
   that doesn't match its action, anything where "correct" depends on
   intent rather than measurement.
4. **The action would structurally edit a read-only master template**
   (`0002`) rather than a governed reusable component or an additive new
   variant. Template revisions are deliberate, explicit actions, never a
   side effect of a fix pass.
5. **Scope itself is ambiguous** — which components are in play, how far
   a fix should extend, whether a gray/white/other button already exists
   somewhere unaudited. Resolve the factual gap before deciding anything.

## Alternatives considered

**Always ask before any fix, to be safe.** Rejected — this is what made
the interactive session slow in places, and most of this project's actual
progress (every color migration, every font fix, every icon snap once the
pattern was proven) came from proceeding on data-certain findings without
re-litigating settled decisions. An agent that asks about everything isn't
autonomous, it's a slower interactive session.

**Never ask; always make the best guess and log it for later review.**
Rejected — several findings this session (the amber colors, the Cancel
shade formula, the gray button's actual location) had no "best guess" that
wouldn't have been a fabricated design decision wearing a compliance-fix
disguise. `0017`'s own history (an invented manual line break caused the
original orphan bug) is a direct precedent for why guessing under
uncertainty creates the next bug rather than avoiding one.

**Base the fix/ask split on blast radius (small edit = fix, large edit =
ask) instead of on whether the value is decided.** Rejected — blast radius
and decidedness are independent. Get Demo's blue-600 fix touched every
instance across the whole file and still needed no question, because the
value was already fully decided; the amber chip touched only 2 chips and
still needed a question, because no value was decided at all. Gating on
blast radius would have blocked the correct immediate fixes and let through
an under-decided one just because it looked small.

## Consequences

- This policy is the classification step (`audit-procedure.md` step 9) any
  future audit — human or agent — runs before fixing anything.
- An agent following this policy will still generate questions; the goal
  isn't zero questions, it's that every question is a genuine gap in the
  doctrine, not a re-litigation of something already decided.
- This policy will need revisiting once real agent runs start — if a
  category of finding keeps landing on the wrong side of this line in
  practice, that's a signal to refine the rule, not to override it ad hoc
  per run.

## Related

- `context/audit-procedure.md` — the full audit checklist this policy's
  classification step belongs to.
- `decisions/0014-agent-guardrails-adapted-from-sdlc-governance-blueprint.md`
  — the three-strike/context-capping rules this policy sits alongside;
  0014 governs *how many times* to retry, this governs *whether to ask*.
- `decisions/0002-band-template-style-lock.md` — the read-only-master rule
  behind stop-and-ask rule 4.
- `decisions/0017-orphan-check-extends-to-any-reflow-causing-change.md` —
  the guessing-under-uncertainty precedent behind rejecting "always guess."
- Memory: `multi-band-page-agent-goal.md` — the Phase 0/1/2 roadmap this
  policy is a prerequisite for (Phase 1: single-band subagent, multi-band
  page agent).
