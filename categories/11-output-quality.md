# Category 11: Output Quality & Usability

**Always applicable.**

**Static ceiling:** 10

---

## Hard Blockers

- [ ] **BLOCKER-1:** Output format is undocumented and demonstrably inconsistent — the skill may return free text, JSON, or nothing depending on the path taken, with no documented pattern.
- [ ] **BLOCKER-2:** Output contains no actionable content — no findings, no next steps, no structured result of any kind.

---

## Critical Gates (2 points each, max 6)

- [ ] **CG-1:** Is the output format documented and consistent — a reader can predict the structure before running the skill?
- [ ] **CG-2:** Is the output actionable — does it tell the user or a downstream agent what happened and what to do next?
- [ ] **CG-3:** Is verbosity appropriate — output is neither excessively noisy (pages of irrelevant text) nor silent (no output at all)?

---

## Quality Gates (1 point each, max 4)

- [ ] **QG-1:** Are error outputs structurally distinguishable from success outputs (not just a different message in the same format)?
- [ ] **QG-2:** Is the output machine-parsable when downstream agent consumption is expected (JSON, structured markdown with predictable headings)?
- [ ] **QG-3:** Are human-friendly explanations included when technical output alone would not be interpretable by a non-expert?
- [ ] **QG-4:** Is output length bounded and predictable — no runaway verbosity on large inputs?

---

## Scoring

```
score = (CG_yes × 2) + (QG_yes × 1)
if BLOCKER-1 OR BLOCKER-2: score = min(score, 3)
```
