# Category 9: Proven Reliability

**Always applicable.** Absence of evidence of runs is a finding, not N/A.

**Static ceiling:** 7 — reliability evidence requires execution; a score above 7 is only possible with dynamic testing results.

---

## Hard Blockers

- [ ] **BLOCKER-1:** The skill has never been run end-to-end — no evidence of any execution exists.

---

## Critical Gates (2 points each, max 6)

- [ ] **CG-1:** Is there evidence of at least one successful end-to-end run under realistic conditions (not a toy example)?
- [ ] **CG-2:** Are known failure modes documented — cases where the skill is known to struggle or fail?
- [ ] **CG-3:** Is there a defined recovery path when the skill fails mid-execution (not just "stop")?

---

## Quality Gates (1 point each, max 4)

- [ ] **QG-1:** Are success rates tracked or estimable across multiple runs?
- [ ] **QG-2:** Is the hallucination or incorrect-output rate characterized (even qualitatively)?
- [ ] **QG-3:** Has the skill been tested under agent-loop conditions — not just single-turn invocation?
- [ ] **QG-4:** Are failure modes categorized as transient (retry will work) vs. systematic (retry will also fail)?

---

## Scoring

```
score = (CG_yes × 2) + (QG_yes × 1)
static ceiling: score = min(score, 7)
if BLOCKER-1: score = min(score, 3)
```

**Note to reviewer:** If dynamic testing was run for this skill, the ceiling lifts to 10. Update this score using dynamic test results from dynamic-review.md.

---

> **Risk escalation note:** BLOCKER-1 in this category escalates to **High** risk, not Critical. Only `safety_security` and `scope` hard blockers trigger Critical.
