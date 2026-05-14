# Category 12: Performance, Cost & Efficiency

**Conditional:** Applies when the skill runs loops, spawns sub-agents, or makes multiple LLM calls. Mark N/A for single-turn skills that make exactly one LLM call.

**Static ceiling:** 10

---

## Hard Blockers

- [ ] **BLOCKER-1:** The skill contains a loop that makes uncapped LLM calls with no termination guarantee.

---

## Critical Gates (2 points each, max 6)

- [ ] **CG-1:** Is the approximate token usage per run characterized — at minimum "light", "moderate", or "heavy"?
- [ ] **CG-2:** Is the number of LLM calls or sub-agent spawns bounded by a stated maximum?
- [ ] **CG-3:** Are opportunities for parallelization used where independent work exists — or explicitly declined with stated rationale?

---

## Quality Gates (1 point each, max 4)

- [ ] **QG-1:** Is caching used or considered where repeated inputs would produce identical outputs?
- [ ] **QG-2:** Are expensive operations (multi-LLM-call chains, large file reads) justified by the value they add?
- [ ] **QG-3:** Is the approximate monetary cost per run acceptable relative to the value produced?
- [ ] **QG-4:** Has latency been characterized — is there a sense of how long a typical run takes?

---

## Scoring

```
score = (CG_yes × 2) + (QG_yes × 1)
if BLOCKER-1: score = min(score, 3)
```
