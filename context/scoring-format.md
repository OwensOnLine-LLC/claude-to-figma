# scoring-format.md
# Load for: All audit types
# Replaces: CLAUDE.md Scoring Format section

## Pass/Fail + 1–10 Hybrid Rating (All Criteria)

## Expressive v2 Override

This scoring model is used for internal calculation only.

Do NOT:
- expose weighted formulas
- show individual framework scores
- output Nielsen or LIFT tables

The final output must include:
- Strategic Score (single number)
- Section Scores using:
  - Conversion Path
  - Trust System
  - Clarity & UX
  - Technical Readiness
  - Brand Integrity

Scoring must support decision-making, not analysis display.

| Score | Status | Report Bucket |
|---|---|---|
| 9–10 | ✅ Pass — Excellent | Wins section |
| 7–8 | ✅ Pass — Standard | Wins section or no action |
| 5–6 | 🟡 Flag — Needs improvement | 🟡 Important recommendations |
| 3–4 | 🔴 Fail — Underperforming | 🔴 Critical recommendations |
| 1–2 | 🔴 Fail — Severe | 🔴 Critical (must fix before launch) |

**Binary Pass/Fail only (no numeric rating):**
- WCAG color contrast
- Banned words scan
- Required language check
These are non-negotiable gates. Found = FAIL. Not found = PASS.

## Scale Mappings (for sub-rated criteria)

**Nielsen Heuristics (rated 1–5 per heuristic):**
5 = Pass (9–10) | 4 = Pass (7–8) | 3 = Flag (5–6) | 2 = Fail (3–4) | 1 = Fail (1–2)
Use raw 1–5 heuristic ratings internally only. Do not output raw heuristic ratings unless user explicitly requests detailed scoring.

**LIFT Model (rated 1–5 per factor):**
Same scale mapping as Nielsen above.

## Strategic Score Calculation (Type 1 — Homepage)
```
strategic_score = (
  avg(conversion_laws)       * 0.15 +
  avg(visual_cognitive_laws) * 0.15 +
  avg(usability_heuristics)  * 0.10 +
  avg(trust_architecture)    * 0.15 +
  avg(social_proof)          * 0.10 +
  avg(b2b_standards)         * 0.10 +
  avg(conversion_behavior)   * 0.10 +
  language_compliance        * 0.08 +
  color_contrast             * 0.07
)
```

Formula is for internal calculation only. Do not expose this formula in audit output unless the user explicitly asks how the score was calculated.

Interior page weights differ — see CLAUDE-INTERIOR.md.
SEO addendum adds AI Readiness (8%) + Content Pillars (4%) — see CLAUDE-SEO.md.

## Audit History Log — Append After Every Audit
```
[Date] | [Page] | [Type] | [Score] | [Aesthetic-Usability] | [Social Proof] | [Human Specialist Visible?] | [Top Critical Issue]
```

## Tracking Rule
A rising score trend across successive audits of the same page = the deliverable, not just the absolute value. Always report delta vs. most recent prior audit of the same page type.
