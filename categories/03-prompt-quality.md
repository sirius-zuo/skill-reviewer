# Category 3: Prompt / Instruction Quality

**Always applicable.**

**Static ceiling:** 10

---

## Hard Blockers

- [ ] **BLOCKER-1:** Instructions are so ambiguous that two engineers reading them would predict contradictory behaviors for the same input.
- [ ] **BLOCKER-2:** The skill has no behavioral instructions — it is only a name and description with nothing guiding what to do.

---

## Critical Gates (2 points each, max 6)

- [ ] **CG-1:** Is the role or persona clearly defined and consistent throughout — no contradictions between sections?
- [ ] **CG-2:** Are ambiguous terms absent or explicitly defined? ("best", "optimize", "appropriate", "reasonable", "properly" must be defined if used, not left to interpretation.)
- [ ] **CG-3:** Is there an explicit rule for resolving instruction conflicts (e.g., "if X and Y conflict, follow X")?

---

## Quality Gates (1 point each, max 4)

- [ ] **QG-1:** Are representative examples provided for non-obvious behaviors (not just the happy path)?
- [ ] **QG-2:** Are hidden assumptions surfaced — things an engineer would need to know that aren't obvious from the domain?
- [ ] **QG-3:** Are instructions structured in sections with clear purposes rather than as one monolithic block?
- [ ] **QG-4:** Could the instructions be extended with a new section without breaking the meaning of existing sections?

---

## Scoring

```
score = (CG_yes × 2) + (QG_yes × 1)
if BLOCKER-1 OR BLOCKER-2: score = min(score, 3)
```

---

## Score Anchors

| Score | What it looks like |
|---|---|
| 9–10 | Role defined, no ambiguity, conflict resolution explicit, examples present, modular structure |
| 7–8 | Instructions clear but missing examples or conflict resolution rule |
| 5–6 | Role implied, some ambiguous terms, monolithic structure |
| ≤3 | Contradictory instructions or no instructions at all |
