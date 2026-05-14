# Category 6: Skill Composability

**Conditional:** Applies when the skill invokes other skills, is designed to be invoked as a sub-skill by others, or produces outputs intended for downstream agent consumption. Mark N/A if the skill is fully standalone with no chaining intent.

**Static ceiling:** 8 — actual chaining behavior requires execution to verify.

---

## Hard Blockers

- [ ] **BLOCKER-1:** The skill produces output in an undocumented or inconsistent format that downstream consumers cannot reliably parse.

---

## Critical Gates (2 points each, max 6)

- [ ] **CG-1:** Is the output format documented precisely enough for another skill to consume it without reading this skill's internals?
- [ ] **CG-2:** Does the skill invoke other skills using the standard Skill tool pattern, not ad-hoc prompt engineering?
- [ ] **CG-3:** Are circular dependency risks ruled out or explicitly documented (does not invoke a skill that invokes this skill)?

---

## Quality Gates (1 point each, max 4)

- [ ] **QG-1:** Is there a stable interface contract (inputs → outputs) that remains valid across minor updates to this skill?
- [ ] **QG-2:** Can this skill be used as a sub-skill without modification (no hard-coded assumptions about being the top-level caller)?
- [ ] **QG-3:** Are side effects (file writes, tool calls) documented for consumers who need to reason about them?
- [ ] **QG-4:** Are versioning or compatibility concerns addressed for consumers that depend on a specific output format?

---

## Scoring

```
score = (CG_yes × 2) + (QG_yes × 1)
if BLOCKER-1: score = min(score, 3)
static ceiling: score = min(score, 8)
```
