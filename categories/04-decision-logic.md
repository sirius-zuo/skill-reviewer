# Category 4: Decision Logic & Workflow

**Conditional:** Applies when the skill has branching logic, loops, or a multi-step workflow. Mark N/A if the skill is a single-step reference or lookup with no branching.

**Static ceiling:** 8 — edge case handling under real execution cannot be fully verified statically.

---

## Hard Blockers

- [ ] **BLOCKER-1:** A loop or iterative process exists with no exit condition or termination guarantee.
- [ ] **BLOCKER-2:** The primary workflow has no fallback — if the main path fails, behavior is completely undefined.

---

## Critical Gates (2 points each, max 6)

- [ ] **CG-1:** Does every loop or iterative process have an explicit, bounded termination condition?
- [ ] **CG-2:** Are fallback, retry, and escalation paths defined for the main workflow branches?
- [ ] **CG-3:** Are dead-end states handled — states where the skill cannot proceed but also cannot exit cleanly?

---

## Quality Gates (1 point each, max 4)

- [ ] **QG-1:** Are the boundaries between deterministic steps (must produce X) and non-deterministic steps (may produce one of X, Y, Z) acknowledged?
- [ ] **QG-2:** Is the maximum total number of steps or LLM calls bounded?
- [ ] **QG-3:** Are conflicting instructions resolved with a defined precedence rule?
- [ ] **QG-4:** Are edge cases in each major decision branch documented?

---

## Scoring

```
score = (CG_yes × 2) + (QG_yes × 1)
if BLOCKER-1 OR BLOCKER-2: score = min(score, 3)
static ceiling: score = min(score, 8)
```

---

## Score Anchors

| Score | What it looks like |
|---|---|
| 8 | All loops bounded, fallbacks defined, dead-ends handled, step count bounded |
| 6–7 | Loops bounded but fallbacks incomplete; or edge cases undocumented |
| 4–5 | Hard blocker not triggered but one major branch has undefined behavior |
| ≤3 | Hard blocker triggered — unbounded loop or no fallback on primary path |
