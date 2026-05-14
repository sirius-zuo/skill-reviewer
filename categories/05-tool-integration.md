# Category 5: Tool Integration & Dependencies

**Conditional:** Applies when the skill makes external tool calls (file reads/writes, API calls, shell commands, browser interactions). Mark N/A if the skill makes no external calls.

**Static ceiling:** 8 — tool failure and rate-limit behavior requires execution to verify.

---

## Hard Blockers

- [ ] **BLOCKER-1:** Tool calls are made with user-supplied input passed directly, without any validation or sanitization.
- [ ] **BLOCKER-2:** There is no error handling of any kind — if a tool fails, the skill behavior is completely undefined.

---

## Critical Gates (2 points each, max 6)

- [ ] **CG-1:** Is input validated or bounded before each tool call (correct type, within expected range, not obviously malformed)?
- [ ] **CG-2:** Are error, timeout, and unavailability scenarios handled — even minimally (surface the error, stop gracefully)?
- [ ] **CG-3:** Is the minimum required permission set for each tool documented?

---

## Quality Gates (1 point each, max 4)

- [ ] **QG-1:** Are tool outputs validated or normalized before use (not blindly trusted as correct data)?
- [ ] **QG-2:** Are idempotent and non-idempotent tool calls distinguished (retrying a write vs. a read has different consequences)?
- [ ] **QG-3:** Is partial failure handled — the case where some tool calls succeed and others fail in a sequence?
- [ ] **QG-4:** Are rate limits or throttling constraints acknowledged for external APIs?

---

## Scoring

```
score = (CG_yes × 2) + (QG_yes × 1)
if BLOCKER-1 OR BLOCKER-2: score = min(score, 3)
static ceiling: score = min(score, 8)
```
