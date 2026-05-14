# AI Agent Skill Review Framework

**Consolidated Best Practices for Reviewing AI Agent Skills**  
*Version 1.0*  
Combining architecture, code, security, and agent-specific best practices.

---

## Core Principles

- Agent skills are **autonomously invoked** in unpredictable contexts.
- Strong emphasis on **narrow scope**, explicit boundaries, robustness, and emergent risks.
- Treat prompts, workflows, tools, and context handling as first-class review artifacts.
- Goal: Move from "vague ambitions" to reliable, production-grade agent capabilities.

---

## 1. Skill Definition & Scope (Design Gate)

- Clear, narrow objective (one-sentence purpose + measurable success criteria)
- Explicit input/output schemas and formats
- Well-defined **boundaries and non-goals** (what the skill must refuse or defer)
- Preconditions, postconditions, and explicitly forbidden actions

**Key Question**: Is this a precise capability or a vague ambition?

---

## 2. Prompt / Instruction Quality

- Clear role definition and stable system behavior
- Instruction hierarchy with explicit conflict resolution rules
- Modular prompt structure (avoid monolithic prompts)
- Elimination of ambiguous language (“best”, “optimize”, “reasonable”, etc.)
- High-quality, representative few-shot examples
- All hidden assumptions documented

**Key Question**: Can another engineer safely understand, modify, and predict the skill’s behavior?

---

## 3. Decision Logic & Workflow Design

- Tool selection, branching, fallback, retry, and escalation logic
- Bounded loops and clear termination conditions
- Dead-end prevention and conflict handling
- Balance between required determinism and acceptable non-determinism

**Key Question**: Does the workflow remain robust or become spaghetti/infinite loops under stress?

---

## 4. Tool Integration & Dependencies

- Clear tool contracts and pre-call input validation
- Output normalization, error handling, timeout management
- Idempotency where relevant
- Rate limiting awareness and partial failure handling
- Dependency auditing and least-privilege permissions

**Key Question**: What happens on malformed responses, rate limits, or malicious tool output?

---

## 5. Context & Memory Management

- Context window budgeting and intelligent summarization strategy
- State persistence vs. transience policy
- Detection and pruning of stale or irrelevant context
- Prevention of context bloat, instruction drift, and memory poisoning

**Key Question**: Does the skill degrade gracefully or accumulate noise until failure?

---

## 6. Reliability & Robustness (Evaluation)

**Test Categories**:
- Happy path
- Edge cases (incomplete, contradictory, or malformed input)
- Adversarial cases (prompt injection, malicious tool outputs, conflicting memory)

**Key Metrics**:
- Completion success rate
- Hallucination rate
- Correct refusal rate
- Recovery rate after failure

**Key Question**: Is it proven under realistic agent-loop conditions?

---

## 7. Safety & Security

- Strong prompt injection resistance and tool output sanitization
- Tool permission boundaries and sandboxing
- Secret handling and credential redaction
- Data exfiltration prevention
- Unsafe action blocking and reversibility mechanisms
- Blast radius analysis for misuse or skill chaining

**Key Question**: Can this skill be abused, escalated, or hijacked?

---

## 8. Output Quality & Usability

- Consistent, structured, and machine-parsable formats
- Appropriate verbosity and actionability
- Human-friendly explanations when needed
- Readiness for downstream agent integration

**Key Question**: Is the output actually useful for both humans and other agents?

---

## 9. Performance, Cost & Efficiency

- Token usage, tool call volume, and monetary cost per run
- Latency profiles (P50, P95)
- Caching, parallelization, and optimization opportunities
- Overall resource footprint

**Key Question**: Is this skill efficient or an expensive hidden loop?

---

## 10. Observability, Debuggability & Maintainability

- Structured logging, traces, and reasoning checkpoints
- Failure categorization and full run reconstruction
- Modular code structure, semantic versioning, and changelogs
- Comprehensive test suites and rollback plans

**Key Question**: Can you debug why a run failed weeks later?

---

## 11. Human Interaction & Alignment

- Clarification seeking and uncertainty handling
- Confirmation flows for risky or high-impact actions
- Progress updates and interruption support
- Review of ethical risks, bias, compliance, and misuse potential

**Key Question**: Does it collaborate well with humans without over- or under-asking?

---

## Review Gates

| Gate                  | Timing                  | Main Focus Areas                          | Recommended Reviewers                |
|-----------------------|-------------------------|-------------------------------------------|--------------------------------------|
| **Design Review**     | Before implementation   | Scope, workflow, risks, architecture      | Architect + Domain Expert            |
| **Behavior Review**   | After implementation    | Prompts, tools, evals, safety, output     | Peer + Security + Agent Specialist   |
| **Operational Review**| Before production       | Observability, cost, monitoring, compliance | Ops + Compliance + Red Team         |

---

## Quick Scoring Checklist (Rate 1–10)

- [ ] Skill Definition & Scope
- [ ] Prompt Quality
- [ ] Workflow & Decision Logic
- [ ] Tool Integration
- [ ] Context Management
- [ ] Reliability & Evals
- [ ] Safety & Security
- [ ] Output Quality
- [ ] Performance & Cost
- [ ] Observability & Maintainability
- [ ] Human Interaction & Alignment

**Target**: Average ≥ 8/10, with no critical category (especially Scope and Safety) below 7.

---

**End of Document**