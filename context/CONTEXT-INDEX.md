# CONTEXT-INDEX.md
# Modular Context Architecture — Owens OnLine FE2 Redesign
# Load only what the audit type requires. Do not load all files simultaneously.

## Load Matrix

| File | Type 1 | Type 1B | Type 2 | Type 3 | Always |
|---|:---:|:---:|:---:|:---:|:---:|
| `scoring-format.md` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `language-rules.md` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `trust-eeat.md` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `conversion-laws.md` | ✓ | ✓ | ✓ | ✓ | — |
| `visual-cognitive-laws.md` | ✓ | ✓ | ✓ | ✓ | — |
| `wcag-contrast-logic.md` | ✓ | ✓ | ✓ | ✓ | — |
| `social-proof-hierarchy.md` | ✓ | ✓ | ✓ | ✓ | — |
| `b2b-persona-matrix.md` | ✓ | ✓ | — | — | — |
| `conversion-behavior.md` | ✓ | ✓ | — | — | — |
| `interior-page-overrides.md` | — | ✓ | — | — | — |
| `geo-citation-logic.md` | — | — | ✓ | ✓ | — |
| `frontend-styles.md` | ✓ | ✓ | ✓ | ✓ | — |

## Token Efficiency Recommendation

### STAYS in CLAUDE.md System Prompt (Always Active)
These are gates and protocols that apply to every single audit — loading on demand would add friction and risk skipping them:
- **Master Criterion — Human Specialist Network** (non-negotiable gate, all types)
- **Audit Gate protocol** (must fire before every audit, no exceptions)
- **Audit Type definitions** (menu presented at start, type must be declared)
- **Pass/Fail + 1–10 scoring format** (governs all output)
- **Audit Archiving / Template Export Protocol** (fires at end of every audit)
- **Banned words list** (5 terms — minimal tokens, binary gate)
- **Required language list** (5 terms — minimal tokens, binary gate)

### MOVES to /context (Load on Demand)
These contain detailed reasoning that only activates during specific audit types. Loading them only when needed saves significant tokens on repeated sessions:

| File | ~Token Savings | When to Load |
|---|---|---|
| `conversion-laws.md` | ~400 tokens | Type 1, 1B, 2, 3 |
| `visual-cognitive-laws.md` | ~450 tokens | Type 1, 1B, 2, 3 |
| `b2b-persona-matrix.md` | ~500 tokens | Type 1, 1B only |
| `conversion-behavior.md` | ~450 tokens | Type 1, 1B only |
| `wcag-contrast-logic.md` | ~350 tokens | All types |
| `geo-citation-logic.md` | ~600 tokens | Type 2, 3 only |
| `social-proof-hierarchy.md` | ~300 tokens | All types |
| `interior-page-overrides.md` | ~400 tokens | Type 1B only |
| `frontend-styles.md` | ~350 tokens | Report generation only |
| `scoring-format.md` | ~300 tokens | All types |
| `language-rules.md` | ~250 tokens | All types |

**Estimated total savings per Type 1 audit:** ~1,800 tokens
**Estimated total savings per Type 2 audit:** ~2,400 tokens
**Estimated total savings per Type 1B audit (interior):** ~2,000 tokens

## Invocation Pattern (Future Automation)
```
Type 1:  load [scoring-format, language-rules, trust-eeat, conversion-laws, visual-cognitive-laws, wcag-contrast-logic, social-proof-hierarchy, b2b-persona-matrix, conversion-behavior, frontend-styles]
Type 1B: load [all Type 1 files + interior-page-overrides]
Type 2:  load [all Type 1 files + geo-citation-logic] - remove b2b-persona-matrix, conversion-behavior
Type 3:  load [all Type 2 files]
```
