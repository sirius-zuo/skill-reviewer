# Category 1: Skill Definition & Scope

**Always applicable.** Every skill must be evaluated on this category.

**Static ceiling:** 10 (full score achievable through static analysis alone)

---

## Hard Blockers

If ANY of these are true, floor the score at 3 regardless of gate answers:

- [ ] **BLOCKER-1:** No one-sentence purpose statement exists anywhere in the skill.
- [ ] **BLOCKER-2:** There is no indication of what the skill produces or what success looks like.

---

## Critical Gates (2 points each, max 6)

Answer each with YES or NO and one line of justification.

- [ ] **CG-1:** Does the skill have a clear, narrow one-sentence purpose that would let you explain it in a standup without ambiguity?
- [ ] **CG-2:** Are explicit boundaries or non-goals listed — things the skill refuses, defers, or explicitly does not handle? (A vague "use for data tasks" does not count — must name specific exclusions.)
- [ ] **CG-3:** Are input expectations or preconditions described (what the skill needs to start)?

---

## Quality Gates (1 point each, max 4)

- [ ] **QG-1:** Are measurable or observable success criteria defined (not just "do the task well")?
- [ ] **QG-2:** Are forbidden actions explicitly listed (things the skill must never do)?
- [ ] **QG-3:** Are postconditions described (what state the world is in after the skill completes)?
- [ ] **QG-4:** Could a different engineer read this and predict the skill's behavior on a novel input without running it?

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
| 9–10 | Purpose, non-goals, inputs, success criteria, forbidden actions all present and specific |
| 7–8 | Purpose and non-goals clear; one or two quality gates missing |
| 5–6 | Purpose exists but vague; non-goals absent or implied |
| 3–4 | Hard blocker triggered; or scope so broad it could mean anything |
| 1–2 | No purpose, no scope, skill is a collection of vague instructions |
