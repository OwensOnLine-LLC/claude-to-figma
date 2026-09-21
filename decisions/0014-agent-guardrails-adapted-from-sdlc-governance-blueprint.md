# 0014. Agent guardrails adapted from the enterprise SDLC governance blueprint — loop safety and context capping bind now, token/cost metering waits for an orchestrator

Status: Accepted (deferred items 4-5 superseded by `0019` — the SOP is
company-mandated and its numeric guardrails are now adopted verbatim, not
still deferred)
Date: 2026-09-14

## Context

An external reference document ("Enterprise Agentic SDLC Governance Blueprint")
was shared, describing guardrails for autonomous coding agents operating across
a CI/CD pipeline in a polyglot microservice codebase: a three-tier token/cost
budget (global lifecycle, single agent run, self-correction iteration), a
Three-Strike Escalation Protocol for build failures, a hard turn/time ceiling,
context-pruning techniques (AST code maps, targeted file pinning, code
squashing), and a Python reference implementation of the guardrail middleware
sitting inside a state-machine orchestrator.

None of that infrastructure exists here. This project runs as a single
interactive Claude Code session doing Figma API calls and file edits, with a
human reviewing and confirming nearly every turn — not an unattended pipeline
compiling code, rebuilding containers, and paging engineers on failure. Porting
the blueprint verbatim would mean adopting mechanisms (per-token cost caps,
container rebuilds, Slack/Jira escalation, a `state: dict` passed between
orchestrator nodes) that have nothing to attach to yet. But the underlying
*principles* — don't loop on the same failure indefinitely, don't dump
unbounded context, escalate to a human instead of guessing forever — are real
and already partially, informally practiced in this project without ever being
written down as a rule anyone is bound to follow.

This ADR is also the first piece of groundwork for the multi-band page agent
and single-band agent work now starting (see the `multi-band-page-agent-goal`
memory note) — an actual orchestrated agent is exactly the context where the
blueprint's token/cost mechanisms *do* become directly portable, so this ADR
draws the line between what binds today and what's a prerequisite for that
work, rather than deferring the whole document.

## Decision

**Binds now, in this interactive session:**

1. **Three-strike rule for any recurring fix.** Attempt 1: diagnose and fix.
   If the same symptom persists on attempt 2, do not retry the same class of
   fix — change approach entirely (this project's own history is the proof
   this matters: the stale-`HUG`-ancestor-width bug in `decisions/0012`
   recurred three separate times before the fix was to walk the *full*
   ancestor chain instead of just the immediate parent). If a third attempt
   still hasn't resolved it, stop, report exactly what was tried and what's
   still wrong, and ask rather than keep guessing. This generalizes what
   `decisions/0012` already learned in the narrow case of sizing/alignment
   bugs into a standing rule for any repeated fix-chase, of any kind.
2. **Context-capping discipline, formalized rather than ad hoc.** Don't load
   a large Figma subtree in full when a shallow query or a targeted lookup
   answers the question — this is already `/create-band`'s cached-template-list
   convention and this session's habit of shallow `.children` reads instead of
   deep recursive `findAll` dumps; the change is that it's now a documented
   rule, not just a habit that happens to hold.
3. **Bounded fix-chase rounds.** A single debugging thread (chasing one bug
   to resolution) gets a bounded number of tool-call rounds before a status
   check-in, rather than open-ended silent iteration — the interactive
   equivalent of the blueprint's turn/time ceiling, enforced by judgment
   rather than a timer since no timer exists here.

**Deferred until an actual orchestrator exists (a Phase 1 prerequisite, not
adopted today):**

4. **Token Burn Management Framework's hard $ / token caps.** These require
   something that can meter cumulative token usage and cost per run and halt
   execution on breach — an interactive session has no mechanism to self-meter
   this. This becomes real infrastructure requirement the day the multi-band
   page agent or single-band agent is built as an actual automated pipeline
   (per the `multi-band-page-agent-goal` memory note), not before.
5. **The Python `TokenAndLoopGuardrail` reference implementation.** Directly
   portable at that point with vocabulary swaps (`compilation_errors` →
   Figma-validation errors, "container rebuild" → re-screenshot-and-verify
   pass, "human_escalation_node" → whatever the new agent's escalation path
   is) — not before, since there's no state-machine for it to sit inside yet.

## Alternatives considered

**Adopt the full blueprint as-is, including the token/cost caps and Python
middleware, immediately.** Rejected — there is nothing for the token-metering
half to attach to yet; adopting it now would mean writing guardrail code that
can't actually observe or halt anything, which is worse than not having it,
since it would look like a real safety mechanism while doing nothing.

**Ignore the document entirely since it doesn't match this project's
architecture.** Rejected — the loop-safety and context-capping principles are
real and already partially practiced; naming them as a binding rule now costs
nothing and directly addresses a real, demonstrated failure mode (the repeated
stale-width bug chase).

**Wait until the single-band/multi-band agents exist before writing anything
down.** Rejected — the three-strike and context-capping rules are useful
immediately, in the current interactive mode, independent of whether any new
agent gets built; there's no reason to gate documenting them on Phase 1
starting.

## Consequences

- Rules 1–3 apply to all work in this session from now on, not just future
  agent-building — including the ongoing `/create-band` and Figma design-system
  work already underway.
- Rules 4–5 are now an explicit, named prerequisite for the multi-band page
  agent and single-band agent work: whoever builds that orchestrator needs to
  design its token/cost metering and adapt the Python guardrail reference
  before (or as part of) shipping it, not as an afterthought.
- This ADR does not itself unblock Phase 1 — the `multi-band-page-agent-goal`
  memory note's Phase 0 gate (harden the rules until an agent following only
  the written doctrine would produce competent output) is a separate
  condition, tracked separately, and should be checked explicitly rather than
  assumed satisfied by this ADR landing.

## Related

- `decisions/0012-responsive-text-container-audit.md` — the narrow,
  domain-specific precedent (sizing/alignment bugs specifically) that rule 1
  above generalizes into a standing rule for any recurring fix.
- Memory: `multi-band-page-agent-goal.md` — the Phase 0/Phase 1/Phase 2
  roadmap this ADR's deferred items (4–5) are a named prerequisite for.
- Source: "Enterprise Agentic SDLC Governance Blueprint" (external document,
  shared 2026-09-14) — the reasoning this ADR translates from a CI/CD
  orchestrator context into an interactive-session context.
