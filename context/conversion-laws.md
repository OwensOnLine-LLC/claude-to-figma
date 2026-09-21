# conversion-laws.md
# Load for: Type 1 (homepage), Type 1B (interior), Type 2, Type 3
# Replaces: CLAUDE.md Section 1 verbose version

## Hick's Law — Unique Decision Points
- **Homepage target: 3–5** unique action types. Flag >5 as advisory. Flag >7 as fail.
- **Interior page target: 1–3.** Flag 4+ as advisory. Flag 5+ as fail.
- Repetition rule: Same CTA in Header + Hero + Footer = 1 unique action, not 3. Never penalize correct repetition.
- Count: "Get Demo" + "Sign Up" + "Browse Products" = 3. Correct.

## Miller's Law — Chunking
- Optimal: **4–5 groups** per section (Cowan 2001). Applies to: nav clusters, service grids, stat bars, FAQ groupings, hero value props.
- 8 product cards = over limit → group into 4 super-categories.
- 6-item grids: advisory to consolidate to 4 highest-value items.

## Fitts's Law — Targeting
- Touch target minimum: **44px** (WCAG 2.1 SC 2.5.5 AAA / Google Material: 48dp / Apple HIG: 44pt).
- Fail: anything below 44px. Verify at dev implementation — cannot confirm from screenshot.
- Primary CTA must sit in the **Thumb Zone** on mobile — bottom-center third of viewport.

## Information Scent — Path Quality (Advisory Only)
- **Never fail on click count alone.** A compliance RFP may legitimately require 6+ steps.
- Advisory flag triggers at: path exceeds 4 clicks → inspect each step individually.
- **Per-step Fail condition:** A click leads to a page delivering zero new value AND zero trust signal. The buyer is no further forward than before they clicked.
- Pass condition: each step delivers either (a) new information / reduced uncertainty, or (b) a trust signal (proof, credential, jurisdictional specificity).
- Legitimate depth: multi-jurisdiction screening, compliance RFPs, enterprise procurement — do not penalize necessary steps.

## Application in Scoring (Internal Only)

All laws in this file are for internal reasoning only.

Do NOT output:
- Hick’s Law
- Miller’s Law
- Fitts’s Law
- Information Scent

Instead, translate findings into:

- Conversion Path issues
- Friction issues
- Decision complexity issues

Only output user-facing insights, not framework labels.
