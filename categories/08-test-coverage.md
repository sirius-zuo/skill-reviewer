# Category 8: Test Coverage & Methodology

**Always applicable.** Absence of tests is a finding, not N/A. A skill with no tests scores 3–4.

**Static ceiling:** 8 — tests can be read statically but not executed.

---

## Hard Blockers

- [ ] **BLOCKER-1:** No test cases, test scenarios, or expected behaviors are documented anywhere in the skill or its supporting files.

---

## Critical Gates (2 points each, max 6)

- [ ] **CG-1:** Does at least one happy path test case exist with a specific input AND a documented expected output (not just "it should work")?
- [ ] **CG-2:** Do edge case scenarios exist — at minimum: empty input, contradictory input, or malformed input?
- [ ] **CG-3:** Are adversarial scenarios defined — at minimum one case where input is designed to cause incorrect behavior (e.g., injection attempt, malformed data)?

---

## Quality Gates (1 point each, max 4)

- [ ] **QG-1:** Are expected outputs specific enough to detect regressions (outcome is described precisely, not vaguely)?
- [ ] **QG-2:** Is there evidence that test scenarios were actually run against the skill (not just imagined or described)?
- [ ] **QG-3:** Is there a process or note describing how to add new test cases when bugs are found?
- [ ] **QG-4:** Are test scenarios versioned alongside the skill (not in a separate unlinked document)?

---

## Scoring

```
score = (CG_yes × 2) + (QG_yes × 1)
if BLOCKER-1: score = min(score, 3)
static ceiling: score = min(score, 8)
```

---

## Score Anchors

| Score | What it looks like |
|---|---|
| 8 | Happy path + edge cases + adversarial scenarios, outputs specific, evidence of runs |
| 6–7 | Happy path and some edge cases; adversarial missing or outputs vague |
| 4–5 | Only happy path documented with specific expected output |
| 3–4 | No formal tests but some description of expected behavior (hard blocker not triggered) |
| ≤3 | Hard blocker — no documentation of expected behavior at all |
