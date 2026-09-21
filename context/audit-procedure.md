# Audit Procedure — the checklist any audit (human or agent) runs

This is the process-facing counterpart to `context/tailwind-tokens.md`: that
file is the *result* (which value), this one is the *procedure* (how to
find, verify, and fix a value, in what order). Both are compiled from the
`decisions/` ADRs — read the linked ADR for the reasoning behind any step
here; this file exists so the steps don't have to be re-derived from 18
files of prose every time.

**Run this whenever auditing or fixing any component, band, or element** —
not just once at project start. Every step below was learned from a real
bug found by skipping it; the ADR cited is where that bug is documented.

## The procedure, in order

1. **Discovery before creation.** Before building or fixing anything, check
   whether a real Figma `COMPONENT`/`COMPONENT_SET` already exists for this
   element — search by name across pages, don't assume a frame convention
   is the only option. Log false-positive matches too (e.g. "CardView" and
   "Due Diligence & Media" were real matches that turned out to be
   unrelated) so the next run doesn't re-discover the same dead end.
   → `0002`, and this session's Button/Card/Media Frame discovery passes.

2. **Measure the actual rendered geometry, not just reported properties.**
   Compute padding from edge positions, gaps from child positions — don't
   trust `itemSpacing`/`padding*` fields alone. A frame with
   `primaryAxisAlignItems: 'SPACE_BETWEEN'` on an auto-hugging parent
   reports a nonzero `itemSpacing` while rendering a 0px gap.
   → `0012` point 3.

3. **Alignment ground truth is the icon, not internal agreement.** For a
   title/description (or value/label) pair sitting under an icon, the
   icon's position is the ground truth for `textAlignHorizontal` — two
   internally-consistent but icon-mismatched texts are still wrong.
   → `0012` point 1.

4. **Full-chain sizing, not just the immediate parent.** Walk every
   ancestor up to the shared column-width ancestor; fixing one `HUG` frame
   isn't enough if another sits above it. Set the text node's own
   `layoutSizingHorizontal` too — fixing ancestors alone doesn't do it.
   → `0012` point 2.

5. **Cross-check every color against the resolved token table**
   (`context/tailwind-tokens.md`'s Color section), even if nothing looks
   visually broken. Raw/unresolved values render fine on their own — they
   only look wrong sitting next to the resolved version. Found twice this
   way in one session (status chips, then Get Demo/Chip) — never assume
   the first raw-color catch was the only one.
   → `0001`, `0008`.

6. **Cross-check font family** against the decided targets (Inter / Inter
   Tight). Use the established stand-in (`Inter Bold` for Inter Tight)
   without re-asking — that question is closed — but state the
   substitution explicitly every time, never silently.
   → `0013`, and the Button/Card font migrations.

7. **Check icon sizing, and how safe a fix is, before touching it.**
   A simple, near-square vector can be resized directly (low distortion).
   A composite/irregular shape (multiple parts, asymmetric bounding box)
   should be wrapped in a fixed-size container instead — resizing it
   directly distorts the art. Check the icon's actual child structure
   before deciding which case it is.
   → `0008`'s Button-icon vs. Card-icon vs. Media-Frame-icon resolutions.

8. **Orphan/line-wrap check after any reflow-causing change** — not only
   when copy is first written. Flag candidates with the height÷lineHeight
   heuristic, then individually read each flagged node's actual last line;
   a whole-band screenshot glance is not sufficient. Fix by joining the
   last two words with a non-breaking space (preserves copy) before
   trimming (loses copy).
   → `0017`.

9. **Classify every finding against the autonomy policy** (`0018`) before
   fixing it — mechanically-certain vs. a genuine judgment call. Don't
   silently pick an answer to a judgment call, and don't stop and ask about
   something the doctrine already decided.

10. **Apply certain fixes immediately; queue judgment calls.** Don't let an
    open judgment call block fixing the certain items sitting next to it.
    Bound every fix-chase to the SOP's Three-Strike protocol (attempt →
    change approach → hard halt) and its 5-tool-call/15-minute window per
    Single Agent Execution Run — both adopted verbatim, not project-tuned.
    → `0019`.

11. **Verify visually after every mutation** — screenshot before and after.
    If the element can appear on more than one background (a white/outline
    element meant for a dark band), verify against both — Figma's default
    white canvas will hide a transparent-fill bug that only shows up on the
    intended backdrop.
    → the `createAutoLayout` white-fill-default bug, caught only because a
    dark backdrop was actually used to check.

12. **Check blast radius on real components.** If the edit touches an
    actual master `COMPONENT` (not a clone), re-screenshot at least one
    live instance elsewhere in the file to confirm the change propagated
    correctly — never assume from the master alone.
    → every Button/Card fix this session.

13. **Annotate the real object, not a floating label.** Attach the
    resolved Tailwind class(es) as a Dev Mode annotation on the master
    component or governed reusable-component node itself. For a
    structurally-repeated set of siblings sharing the same value, annotate
    only the first with a count note — never one copy per sibling.
    → `0007`, `0016`.

14. **Update the doctrine in the same pass.** The relevant ADR and
    `context/tailwind-tokens.md` change together with the fix — a decision
    that lands only in conversation is a decision that doesn't exist next
    session.
    → `decisions/README.md`'s relationship note.

15. **Confirm untouched things stayed untouched.** If the intent was to fix
    a clone, a specific variant, or a subset of instances, re-check that
    the read-only master (or the instances deliberately left alone) are
    still exactly as they were — don't assume a scoped edit stayed scoped.
    → `0002`'s read-only-master rule.

## Related

- `context/tailwind-tokens.md` — the result-layer counterpart to this
  procedure file.
- `decisions/0018-agent-autonomy-policy.md` — what to do differently
  depending on where a finding lands (fix now vs. ask), referenced in
  step 9.
- `decisions/README.md` — the ADR-vs-result architecture both this file
  and `tailwind-tokens.md` follow.
