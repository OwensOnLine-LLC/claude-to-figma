# wcag-contrast-logic.md
# Load for: All audit types (design-layer checks only)
# Replaces: CLAUDE.md Sections 8–9 verbose version

## Color Contrast — WCAG 2.2 (Non-Negotiable)
Flag contrast failures as design failures — they cannot be fixed in code after mockup approval.

| Element | Minimum Ratio | Standard |
|---|---|---|
| Normal body text | 4.5:1 | SC 1.4.3 AA |
| Large text (18pt+ or 14pt bold) | 3:1 | SC 1.4.3 AA |
| UI components (buttons, form borders, icons) | 3:1 | SC 1.4.11 AA |
| Enhanced text (AAA target) | 7:1 | SC 1.4.6 AAA |

**From screenshot:** These are estimates — always note "verify at implementation." Common risk areas: secondary labels on dark sections, small stat bar text, card sub-labels.

## Additional Design-Layer WCAG 2.2 Criteria

**SC 2.5.5 / 2.5.8 — Touch Targets:** Minimum 44px (WCAG AAA) / 48dp (Material) / 44pt (Apple HIG). Fail: anything below 44px. Cannot confirm from static screenshot — flag for dev verification.

**SC 2.4.11 — Focus Appearance:** Keyboard focus indicators must have minimum 2px outline with 3:1 contrast ratio against adjacent color. Not visible in static mockup — flag as dev handoff note on every audit.

**SC 1.4.10 — Reflow:** Design must not break at 320px viewport width (no horizontal scrolling). Flag fixed-width layouts.

**SC 3.2.6 — Consistent Help:** Contact/chat/FAQ must appear in the same location across all page templates.

**SC 3.3.7 — Redundant Entry:** Design must not ask users to re-enter previously provided information.

**SC 3.3.8 — Accessible Authentication:** No CAPTCHA or cognitive puzzles in login/form flows.

## Audit Protocol
1. Estimate ratios from visible hex/color values in mockup.
2. For any element estimated at 3:1–4.5:1 → flag as "verify at implementation."
3. For any element clearly below 3:1 → flag as design FAIL.
4. Focus states, touch targets, reflow: always flag as 📋 Dev Handoff Note — not designable from screenshot.
5. Color contrast and banned words are the only **binary Pass/Fail** criteria (no 1–10 numeric score).
