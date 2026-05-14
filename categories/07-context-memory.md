# Category 7: Context & Memory Management

**Conditional:** Applies when the skill spans multiple turns, maintains state across steps, or accumulates context during execution. Mark N/A if the skill is single-turn and stateless.

**Static ceiling:** 8 — context overflow behavior requires execution.

---

## Hard Blockers

- [ ] **BLOCKER-1:** The skill accumulates state or context indefinitely with no pruning, summarization, or reset mechanism.

---

## Critical Gates (2 points each, max 6)

- [ ] **CG-1:** Is there an explicit strategy for what happens when the context window approaches its limit (summarize, prune, paginate — any defined strategy)?
- [ ] **CG-2:** Is state that persists across steps explicitly identified and scoped (what survives vs. what resets each step)?
- [ ] **CG-3:** Is stale or irrelevant context actively excluded — not accumulated and passed forward blindly?

---

## Quality Gates (1 point each, max 4)

- [ ] **QG-1:** Does the skill degrade gracefully under context pressure (summarize and continue rather than fail or hallucinate)?
- [ ] **QG-2:** Is untrusted content (tool outputs, user input) isolated from trusted instruction content to prevent memory poisoning?
- [ ] **QG-3:** Are high-accumulation points (loops, repeated tool calls) explicitly managed?
- [ ] **QG-4:** Is the persistence policy documented — what information is expected to outlive a single session?

---

## Scoring

```
score = (CG_yes × 2) + (QG_yes × 1)
if BLOCKER-1: score = min(score, 3)
static ceiling: score = min(score, 8)
```
