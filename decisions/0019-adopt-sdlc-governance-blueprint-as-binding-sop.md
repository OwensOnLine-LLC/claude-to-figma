# 0019. The Enterprise Agentic SDLC Governance Blueprint is adopted as this project's binding SOP, verbatim

Status: Accepted
Date: 2026-09-21

## Context

`decisions/0014` adapted this same document's *principles* (three-strike
escalation, context-capping discipline, bounded fix-chase) into binding
rules for the interactive session, while explicitly deferring its *numeric*
guardrails (token/cost caps, the Python `TokenAndLoopGuardrail` middleware)
until "an actual orchestrator exists" — reasoning that adopting hard
$/token caps with nothing able to observe or enforce them would be worse
than no cap at all, a false sense of safety.

Two things changed since then. First, this project is now actively
building toward that orchestrator (the `multi-band-page-agent-goal`
Phase 1 work — the single-band subagent and multi-band page agent).
Second, and decisively: the user confirmed this document is **company
mandated** — a Standard Operating Procedure, not an optional reference this
project gets to selectively recalibrate. Proposed instead was a
translated, tool-call-count-based version of the budget, scaled to this
project's actual observed usage (a Button audit costing roughly 40 tool
calls, far below the blueprint's compilation-scale token figures). That
proposal is **rejected** — an SOP is followed as written; this project
doesn't get to decide its numbers don't apply at our scale. The only
translation applied below is vocabulary (this environment has no
compilers, containers, or Jira), never the figures themselves.

## Decision

**Adopt the blueprint's numeric guardrails verbatim, with only vocabulary
mapped to this environment — never the numbers themselves adjusted:**

### 1. Token Burn Management Framework — adopted exactly as specified

| Boundary Layer (blueprint term → this project's term) | Max Ingestion | Max Output | Hard Financial Cap |
|---|---|---|---|
| Global Lifecycle / Full Ticket Loop → **a full multi-component audit sweep** | 10,000,000 tokens | 250,000 tokens | $50.00 USD / run |
| Single Agent Execution Run → **one component's full audit** (e.g. one Button or Card pass) | 2,000,000 tokens | 30,000 tokens | $10.00 USD / run |
| Self-Correction Iteration (Per Attempt) → **one fix-chase attempt on one recurring finding** | 500,000 tokens | 8,000 tokens | $2.00 USD / attempt |

### 2. Three-Strike Escalation Protocol — reaffirmed unchanged from `0014`

- **Strike 1**: attempt the fix directly.
- **Strike 2**: if the same symptom persists, don't retry the same class of
  fix — change approach entirely.
- **Strike 3**: hard halt. Save all diagnostic context (the specific nodes
  touched, the fixes attempted, the current state) and escalate — via the
  findings-report mechanism already decided (not Slack/Jira, which this
  environment doesn't have; see vocabulary mapping below).

### 3. Maximum Window Constraints — adopted exactly, mapped to this
environment's units

No individual **Single Agent Execution Run** ("ticket") may execute more
than **5 cumulative reasoning turns**, mapped here as **5 tool-call
rounds**, or remain active more than **15 minutes** wall-clock — whichever
comes first. Breaching either triggers a hard halt of that run, exactly as
specified.

### 4. Context Capping & Repository Pruning — reaffirmed unchanged from `0014`

- AST Anchors → this project's shallow-metadata-query convention (read
  structure/names/counts, not full recursive dumps).
- Targeted File Pinning → reading only the specific node IDs relevant to
  the current fix, not whole pages.
- Automated Code Squashing → cached reference tables (e.g. `SKILL.md`'s
  template/component cache) standing in for unrelated modules.

### 5. Production Guardrail Reference — ported with vocabulary swaps only

```python
# ==============================================================================
# PROJECT AGENTIC AUDIT GUARDRAIL MIDDLEWARE
# Ported from the Enterprise Agentic SDLC Governance Blueprint, vocabulary
# swapped for a Figma design-system audit context. Numbers unchanged.
# ==============================================================================
class TokenAndLoopGuardrail:
    def __init__(self, max_tokens=500_000, max_iterations=3, cost_per_k_token=0.015):
        # max_tokens/max_iterations default to the Self-Correction Iteration
        # tier (one fix-chase attempt) — instantiate per boundary layer.
        self.max_tokens = max_tokens
        self.max_iterations = max_iterations  # the three-strike count
        self.cost_per_k_token = cost_per_k_token

    def verify_safety_limits(self, state: dict) -> dict:
        """Evaluates current run consumption before allowing the next tool call."""
        current_tokens = state.get("cumulative_token_count", 0)
        current_loop = state.get("fix_attempt_count", 0)  # was agent_iteration_count
        estimated_cost = (current_tokens / 1000) * self.cost_per_k_token

        # 1. Assert Token/Cost Caps
        if current_tokens >= self.max_tokens:
            state["findings_log"].append(  # was compilation_errors
                f"HARD HALT: token budget exceeded ({current_tokens} tokens, "
                f"est. ${estimated_cost:.2f})."
            )
            state["human_approved"] = False
            state["next_step"] = "write_findings_report_and_halt"  # was human_escalation_node
            return state

        # 2. Assert Three-Strike Loop Constraint
        if current_loop >= self.max_iterations:
            state["findings_log"].append(
                f"HARD HALT: fix-chase attempts ({self.max_iterations}) exhausted "
                f"on this finding."
            )
            state["human_approved"] = False
            state["next_step"] = "write_findings_report_and_halt"
            return state

        # 3. Validation passed — advance
        state["fix_attempt_count"] += 1
        return state
```

## Vocabulary mapping (translation only, not a numeric adjustment)

| Blueprint term | This project's equivalent |
|---|---|
| Ticket / Single Agent Execution Run | One component's full audit run |
| Reasoning turn | One tool call |
| Container rebuild | A re-screenshot-and-verify pass |
| `compilation_errors` | `findings_log` (this project has no compiler) |
| Slack webhook / Jira notification | The findings-report doc/page (already decided) |
| Sandbox runner | This session/agent's own execution context |

## Alternatives considered

**Recalibrate the numeric caps to this project's actual observed usage**
(proposed prior to this ADR — e.g. 5/40/200 tool calls per tier). Rejected
per explicit instruction: this document is a company-mandated SOP, and
following an SOP means adopting its figures as written, not substituting
project-specific numbers because the mandated ones don't match this
project's current scale.

**Defer the numeric guardrails again, as `0014` originally did.** Rejected
— that deferral was conditioned on "until an actual orchestrator exists,"
and more decisively, the SOP's mandatory status means it isn't this
project's call to keep deferring it.

## Consequences

- **The 5-reasoning-turn (5 tool-call) cap per Single Agent Execution Run
  is materially tighter than this session's own historical usage** —
  Button's full audit alone used roughly 40 tool calls end to end. Under
  this SOP, work at that scale must be split across multiple Single Agent
  Execution Runs (multiple "tickets"), each restarting the 5-call/15-minute
  window, rather than completed as one continuous run. This is a real,
  binding operational consequence of adopting the SOP verbatim, not an
  oversight — flagged here so it isn't a surprise the first time a run
  halts mid-audit.
- The token/dollar caps are unlikely to bind given this project's actual
  usage profile (nowhere near compilation-scale token counts) — they are
  formally adopted and satisfied regardless, per the SOP's own terms.
- `0014`'s deferred items are now superseded by this ADR, not still open —
  the numeric guardrails it deferred are the ones adopted here.
- Any future single-band or multi-band agent must implement the
  `TokenAndLoopGuardrail` structure above (or an equivalent) before running
  unattended — this is now a hard prerequisite, not a suggestion.

## Related

- `decisions/0014-agent-guardrails-adapted-from-sdlc-governance-blueprint.md`
  — the principles-only adoption this ADR completes with real numbers.
- `decisions/0018-agent-autonomy-policy.md` — the fix-vs-ask classification
  this ADR's Three-Strike protocol and window constraints sit alongside;
  0018 governs *whether* to ask, this ADR governs *how long* to try first.
- `context/audit-procedure.md` — where the fix-chase ceiling (step 9-10)
  should cite this ADR's 5-turn/three-strike figures directly.
- Source: "Enterprise Agentic SDLC Governance Blueprint" (company-mandated
  SOP, PDF, shared 2026-09-14 and re-confirmed 2026-09-21).
