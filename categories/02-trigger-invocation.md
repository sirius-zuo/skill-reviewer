# Category 2: Trigger & Invocation Design

**Always applicable.** Every skill must have a trigger that determines when it is invoked.

**Static ceiling:** 8 — conflict testing against currently installed skills requires a live environment and cannot be done purely from text.

---

## Hard Blockers

- [ ] **BLOCKER-1:** The frontmatter `description` field is absent or empty.
- [ ] **BLOCKER-2:** The trigger description is a single generic phrase that could apply to any skill (e.g., "use for tasks", "helps with things", "processes data").

---

## Critical Gates (2 points each, max 6)

- [ ] **CG-1:** Is the trigger description specific enough that a reader could confidently say when NOT to use this skill? (Must include concrete conditions or exclusions, not just a use-case description.)
- [ ] **CG-2:** Is it clear what inputs or arguments the skill accepts, including which are required vs. optional?
- [ ] **CG-3:** Does the description cover the primary use case without being broad enough to catch unrelated tasks?

---

## Quality Gates (1 point each, max 4)

- [ ] **QG-1:** Are explicit non-trigger examples provided (what does NOT invoke this skill)?
- [ ] **QG-2:** Does the trigger description avoid using verbs or nouns that overlap significantly with other known skills in the same collection?
- [ ] **QG-3:** Are argument defaults and optional parameters documented?
- [ ] **QG-4:** Does the trigger description accurately describe what the skill actually does (verified by reading the skill body)?

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
| 8 | Precise trigger, non-triggers explicit, arguments documented, no obvious conflicts |
| 6–7 | Trigger is clear but non-triggers absent or arguments undocumented |
| 4–5 | Trigger is broad; could fire on unrelated tasks |
| ≤3 | Hard blocker — description absent or a single generic phrase |
