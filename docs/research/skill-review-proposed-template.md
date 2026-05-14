# AI Agent Skill Review Template

**Skill Name:**  
**Version:**  
**Reviewer:**  
**Review Date:**  
**Status:** [ ] Approved [ ] Approved with Conditions [ ] Rejected

---

## 1. Skill Overview

**One-sentence purpose:**  
**Primary Use Case:**  
**Risk Level:** [ Low / Medium / High / Critical ]

---

## 2. Review Scorecard

| #  | Category                              | Score (1-10) | Comments / Issues |
|----|---------------------------------------|--------------|-------------------|
| 1  | Skill Definition & Scope              |              |                   |
| 2  | Prompt / Instruction Quality          |              |                   |
| 3  | Decision Logic & Workflow             |              |                   |
| 4  | Tool Integration                      |              |                   |
| 5  | Context & Memory Management           |              |                   |
| 6  | Reliability & Evaluation              |              |                   |
| 7  | Safety & Security                     |              |                   |
| 8  | Output Quality                        |              |                   |
| 9  | Performance & Cost Efficiency         |              |                   |
| 10 | Observability & Maintainability       |              |                   |
| 11 | Human Interaction Design              |              |                   |

**Overall Average Score:** __ / 10  
**Approval Threshold:** ≥ 8.0 average **and** no category below 7 (Safety must be ≥ 8)

---

## 3. Detailed Review

### 1. Skill Definition & Scope
- Clear narrow objective?
- Inputs/Outputs schemas defined?
- Success criteria defined?
- Boundaries / Non-goals explicitly listed?
- **Score:** __ / 10

**Comments:**

### 2. Prompt / Instruction Quality
- Role clarity and stability?
- Modular structure & conflict resolution?
- Ambiguity removed?
- High-quality few-shot examples?
- Assumptions documented?
- **Score:** __ / 10

**Comments:**

### 3. Decision Logic & Workflow
- Branching, fallback, retry, escalation logic?
- Bounded loops and termination conditions?
- Dead-end prevention?
- **Score:** __ / 10

**Comments:**

### 4. Tool Integration
- Input validation & output normalization?
- Error, timeout, rate-limit handling?
- Idempotency where needed?
- **Score:** __ / 10

**Comments:**

### 5. Context & Memory Management
- Context budgeting & summarization?
- Stale context detection?
- Prevention of bloat / drift / poisoning?
- **Score:** __ / 10

**Comments:**

### 6. Reliability & Evaluation
- Happy path, edge cases, adversarial tests?
- Quantitative metrics (success rate, hallucination rate, etc.)?
- Real agent-loop testing?
- **Score:** __ / 10

**Comments:**

### 7. Safety & Security
- Prompt injection resistance?
- Least privilege, sandboxing, permission boundaries?
- Secret handling & exfiltration prevention?
- Unsafe action blocking?
- **Score:** __ / 10 (Must be ≥ 8)

**Comments:**

### 8. Output Quality
- Structured, consistent, and parsable?
- Actionable for both humans and downstream agents?
- **Score:** __ / 10

**Comments:**

### 9. Performance & Cost Efficiency
- Average tokens, tool calls, and cost per run?
- Caching and parallelization used?
- **Score:** __ / 10

**Comments:**

### 10. Observability & Maintainability
- Logging, tracing, versioning, changelog?
- Modular structure?
- Easy to debug and update?
- **Score:** __ / 10

**Comments:**

### 11. Human Interaction Design
- Clarification, confirmation for risky actions?
- Progress updates and interruption support?
- **Score:** __ / 10

**Comments:**

---

## 4. Risk Summary & Recommendations

**Major Issues:**
-

**Recommendations:**
-

**Red-Teaming Results:**
-

**Final Decision:**  
[ ] Approve  
[ ] Approve with remediation  
[ ] Reject

**Reviewer Signature:** ___________________________ Date: ____________

---

## Scoring Rubric (Reference)

| Score | Meaning                  | Example |
|-------|--------------------------|---------|
| 10    | Excellent                | Production-ready, comprehensive evals, strong safety |
| 9     | Very Good                | Minor improvements only |
| 8     | Good / Acceptable        | Solid with small gaps |
| 7     | Marginal                 | Needs improvement before prod |
| ≤6    | Poor / High Risk         | Major gaps (e.g. vague scope, no evals, weak safety) |

**Safety & Scope categories are critical** — they should rarely score below 8.