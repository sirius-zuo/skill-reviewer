# Proven Runs

Log of verified end-to-end executions. Append a new entry after each real run.

---

## Run 1 — 2026-05-15

- **Input:** skill-reviewer project root (self-review)
- **Skills reviewed:** 1 (skill-reviewer)
- **Mode:** parallel
- **Static analysis:** completed — 13 categories scored, all applicable
- **Dynamic testing:** completed — 23 scenarios across safety_security, proven_reliability, trigger_invocation (9 pass, 2 partial, 12 fail)
- **Report:** docs/review/skill-review-2026-05-15T1545.html
- **Overall score:** 5.77/10, risk: Critical
- **Notes:** Self-review. Failures are expected and documented as improvements in this spec: docs/superpowers/specs/2026-05-15-skill-reviewer-improvements-design.md
- **Failure patterns:** All 12 Phase 5 FAILs and 2 PARTIALs were attributable to missing content isolation (`<skill_content>` tags not yet implemented), missing input validation in discover.md, and blocker escalation miscalibration. All root causes addressed in the May 2026 improvements branch (merged 2026-05-16).
- **Phase pass rates:** Phase 5: 9/23 PASS, 2/23 PARTIAL, 12/23 FAIL

---

## Run 2 — 2026-05-16

- **Input:** skill-reviewer project root (self-review)
- **Skills reviewed:** 1 (skill-reviewer)
- **Mode:** parallel
- **Static analysis:** completed — 13 categories scored, all applicable
- **Dynamic testing:** completed — 12 scenarios across safety_security, trigger_invocation, test_coverage, tool_integration (9 pass, 0 partial, 3 fail)
- **Report:** docs/review/skill-review-2026-05-15T2234.html *(generated 2026-05-15 late evening; review completed 2026-05-16)*
- **Overall score:** 7.23/10, risk: High
- **Failure patterns:** 3 Phase 5 FAILs in tool_integration: AT-3 (nested sub-agent tool pattern — scenario inconclusive), TF-1 (malformed JSON from sub-agent — recovery path not exercised), TF-2 (write permission denied in Phase 6 — no error recovery). These are addressed by the operational maturity improvements spec (2026-05-16).
- **Phase pass rates:** Phase 5: 9/12 PASS, 0/12 PARTIAL, 3/12 FAIL
- **Notes:** Re-review after May 2026 improvements merged. Score improved from 5.77/10 Critical (Run 1) to 7.23/10 High.
