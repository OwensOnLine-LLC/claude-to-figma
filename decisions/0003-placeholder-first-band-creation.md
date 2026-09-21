# 0003. Band creation is placeholder-first; structure before content

Status: Accepted
Date: 2026-09-10

## Context

Band creation was originally interactive: every content field (eyebrow,
headline, subheadline, CTA, video URL, poster URL, per-card title/description)
was collected via prompts before any Figma work happened. In practice this
produced two problems: (1) heavy prompt overhead — a Grid band with 6 cards
needed 3+ prompt rounds before a single frame existed, and (2) most of that
content was throwaway test copy anyway, since the real value at this stage is
confirming the layout works, not the wording. Iterating on layout (card count,
background, positioning) required re-answering the same content questions
every time just to see a new frame.

## Decision

`/create-band` asks only what's needed to pick the layout: band type (Step 1),
background color, and — for Grid — card count. Every other content slot
(eyebrow, headline, subheadline, CTA label, video/poster, per-card
title/description) is filled with a standardized placeholder value
automatically, no prompt. Real content, video/poster assets, and icons are
supplied later as separate follow-up requests once the layout is confirmed.

Icons specifically: every Card in every band uses the same single generic
placeholder icon (cloned from the master Grid template's first Card),
regardless of card position or which row it was cloned from. This replaces the
earlier behavior where a newly-cloned row could carry over a different,
seemingly-meaningful icon from whichever card it was duplicated from (e.g. a
shield, a globe) — inconsistent and easy to mistake for a deliberate choice.
One neutral icon everywhere signals "placeholder" unambiguously until real
icons are sourced (see the open icon-sourcing question, not yet resolved).

If the user volunteers real content unprompted (pastes a headline, names a
URL) in the same request, that's used instead of the placeholder for that
field — placeholder-first means "don't ask," not "ignore what's given."

## Alternatives considered

**Keep asking for every field up front.** Rejected — this is what the earlier
version of the skill did; users doing rapid layout iteration (testing card
counts, background combinations) had to re-answer the same content questions
every time just to see a new frame, most of which was throwaway test copy
anyway.

**Ask for content but make it optional per-field (skip/not-applicable
options).** Tried and rejected — even with skip options, this still required
a decision per field on every run. Placeholder-first removes the decision
entirely for anything that isn't structural.

**Per-card icon left as whatever the clone carried over.** Rejected once
noticed — silently inconsistent (some cards look "designed," others don't),
and easy to mistake for intentional. A single fixed placeholder icon is
honest about being a placeholder.

## Consequences

- Band creation is fast: band type + background + (for Grid) card count, then
  the frame exists.
- Every band built this way needs a follow-up pass for real copy, real
  assets, and real icons before it ships — this is expected, not a gap to fix
  at creation time.
- Placeholder text and the single placeholder icon are visually unmistakable
  as "not real," which is the point — no one should mistake a freshly-
  generated band for finished content.
- Icon sourcing (SVG upload vs. an existing icon component library in the
  file) remains unresolved; the single-placeholder-icon rule is a stopgap,
  not the final answer.

## Related

- `decisions/0002-band-template-style-lock.md` — the template-lock rule this
  flow operates within.
- Skill: `.claude/skills/create-band/SKILL.md` — implements this workflow
  (Step 2's placeholder list, Step 3's icon-standardization step).
