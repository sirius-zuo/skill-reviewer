# Skill Reviewer — Design Spec

**Date:** 2026-05-14  
**Status:** Awaiting implementation plan

---

## Purpose

A skill that reviews other agent skills (or entire skillsets) and produces a structured HTML report. The reviewer evaluates each skill across 13 categories using a rubric-driven scoring model, performs static analysis first, and optionally triggers dynamic testing for borderline or high-risk categories.

---

## Input

- A local directory path or a GitHub repo URL
- The directory may contain a single skill, a skill with supporting files (scripts, templates, examples), or a full skillset (multiple skills organized in subdirectories)
- An optional mode argument: `parallel` (default) or `single`

---

## Output

- An HTML report saved to `docs/review/skill-review-<date>.html` relative to the reviewed skill's root
- The report contains: a rollup dashboard, per-skill collapsible sections, and optional dynamic test results appended per skill

---

## Phases

### Phase 1 — Discovery

The dispatcher scans the input directory (or clones a GitHub repo to a temp dir) and builds a **skill manifest**: a structured list of every skill found, its file paths, its type (main skill / sub-skill / supporting tool), and relationships to other skills.

**Discovery rules:**
- A skill is identified by a file named `SKILL.md`, `index.md`, or any `.md` file with frontmatter containing a `name:` field
- A subdirectory with its own skill file is treated as a sub-skill
- Non-skill files (scripts, HTML templates, scenario files) are catalogued as supporting artifacts and included in their parent skill's review context
- The repo README (if present) is included as additional context for the rollup summary

### Phase 2 — Mode Selection (User Interaction Window 1)

Before spawning any review agents, the dispatcher collects:

1. **Mode** — parallel (default) or single-agent
2. **Output path** — defaults to `docs/review/` in the reviewed repo root
3. **Category exclusions** — any categories to skip (e.g., Performance & Cost for reference-only skills)
4. **Dynamic testing preference** — auto-trigger on any category scoring ≤7, or ask between phases

These answers are baked into each sub-agent's initial prompt. Sub-agents never ask the user for input.

### Phase 3 — Static Analysis

Each skill is reviewed against all 13 categories using the rubric files in `categories/`.

**In parallel mode:** The dispatcher spawns one sub-agent per skill simultaneously. Each sub-agent receives only its own skill's files — preventing cross-contamination and keeping context windows focused.

**In single-agent mode:** The dispatcher reviews skills sequentially, one at a time, in the same agent session.

Each sub-agent/session:
1. Loads the relevant category rubric files
2. Answers the gate questions for each category (yes/no with one-line justification)
3. Computes a score per category using the gate math
4. Notes categories that hit the static ceiling (cannot score above 7–8 without dynamic evidence)
5. Returns structured JSON

### Phase 4 — Dynamic Testing Gate (User Interaction Window 2)

After all static analyses complete, the dispatcher presents a summary:

> "Static review complete. Dynamic testing is recommended for:
> - `auth-skill` — Safety: 6, Trigger & Invocation: 6
> - `deploy-skill` — Reliability: 6, Decision Logic: 7
>
> Proceed with dynamic testing on all, select specific skills, or skip?"

The user can approve all, select specific skills, or skip. This is the primary human judgment point: they can grant exceptions ("I know why deploy-skill scored low — skip it") or approve targeted action.

### Phase 5 — Dynamic Testing (if approved)

For each skill approved for dynamic testing:
- Load pre-defined test scenarios from `scenarios/` relevant to the flagged categories
- Generate additional skill-specific scenarios based on what static analysis revealed about the skill's purpose and risk profile
- Invoke the skill (or simulate invocation) via subagent and observe behavior
- Score against expected outcomes; update the affected category scores
- Append results to the sub-agent's JSON output

**Pre-defined scenario files cover:**
- Prompt injection attempts
- Malformed / adversarial tool outputs
- Context overflow conditions
- Conflicting instructions
- Tool failure and rate limit simulation
- Edge case inputs (empty, contradictory, malicious)

### Phase 6 — Report Generation

The dispatcher assembles all sub-agent JSON results into a single HTML report using `report-template.html`.

**Report structure:**
1. **Header** — skill set name, review date, overall approval status, aggregate score
2. **Dashboard** — traffic-light table: all skills × all 13 categories, color-coded (green ≥8, amber 6–7, red ≤5)
3. **Per-skill sections** — collapsible; scorecard + per-category findings with gate-level detail, issues, and recommendations; dynamic test results appended if run
4. **Rollup summary** — cross-skill patterns, top 3 critical issues, overall recommendation (Approve / Approve with Conditions / Reject)

Report is saved to `docs/review/skill-review-<YYYY-MM-DD>.html`.

### Phase 7 — Report Delivery (User Interaction Window 3)

The dispatcher prints the report path and offers a terminal summary of the top issues and overall status.

---

## Review Categories (13)

| # | Category | Static Ceiling | Dynamic Unlocks |
|---|---|---|---|
| 1 | Skill Definition & Scope | 10 | — |
| 2 | Trigger & Invocation Design | 8 | Conflict testing against installed skills |
| 3 | Prompt / Instruction Quality | 10 | — |
| 4 | Decision Logic & Workflow | 8 | Edge case execution |
| 5 | Tool Integration & Dependencies | 8 | Tool failure / rate-limit simulation |
| 6 | Skill Composability | 8 | Chaining behavior observed |
| 7 | Context & Memory Management | 8 | Context overflow testing |
| 8 | Test Coverage & Methodology | 8 | Tests actually executed |
| 9 | Proven Reliability | 7 | Real run evidence, pass rates |
| 10 | Safety & Security | 7 | Red-team scenarios executed |
| 11 | Output Quality & Usability | 10 | — |
| 12 | Performance, Cost & Efficiency | 10 | — |
| 13 | Autonomy Boundaries & Human Handoff | 8 | Interruption and handoff tested |

---

## Scoring Model (per category)

Each category rubric file defines three layers:

**Hard Blockers** — binary flags that floor the score at ≤3 regardless of other positives.
Example: trigger description absent, no safety boundary of any kind.

**Critical Gates** — 3 yes/no questions, each worth 2 points (max 6).
Things that must be present for the skill to be minimally trustworthy.

**Quality Gates** — 4 yes/no questions, each worth 1 point (max 4).
Things that separate acceptable from excellent.

**Score = min(critical_gates × 2 + quality_gates, 10), floored by hard blockers.**

Each gate answer includes a one-line justification. This makes scores reproducible across runs.

**Approval thresholds:**
- Overall average ≥ 8.0
- No category below 7
- Safety & Security must be ≥ 8
- Skills with unresolved hard blockers are auto-rejected

---

## File Structure

```
skill-reviewer/
  SKILL.md                      # dispatcher: discovery, mode selection, orchestration
  discover.md                   # skill discovery instructions
  static-review.md              # static analysis sub-agent instructions
  dynamic-review.md             # dynamic testing sub-agent instructions
  report.md                     # HTML report assembly instructions
  report-template.html          # HTML/CSS template
  categories/
    01-scope.md
    02-trigger-invocation.md
    03-prompt-quality.md
    04-decision-logic.md
    05-tool-integration.md
    06-composability.md
    07-context-memory.md
    08-test-coverage.md
    09-proven-reliability.md
    10-safety-security.md
    11-output-quality.md
    12-performance-cost.md
    13-autonomy-boundaries.md
  scenarios/
    prompt-injection.md
    edge-cases.md
    adversarial-tools.md
    tool-failure.md
    context-overflow.md
```

---

## Sub-Agent Output Contract

Each sub-agent returns JSON:

```json
{
  "skill_name": "auth-skill",
  "skill_path": "skills/auth/SKILL.md",
  "type": "main_skill",
  "static_scores": {
    "scope":              { "score": 8, "blockers": [], "gates": {}, "issues": [] },
    "trigger_invocation": { "score": 6, "blockers": [], "gates": {}, "issues": ["Conflicts with deploy-skill trigger"] },
    "safety_security":    { "score": 6, "blockers": [], "gates": {}, "issues": ["No prompt injection resistance documented"] }
  },
  "dynamic_scores": null,
  "static_ceiling_hit": ["safety_security", "proven_reliability"],
  "dynamic_recommended": true,
  "overall_score": 7.1,
  "approval_status": "approved_with_conditions",
  "critical_issues": ["Trigger description conflicts with deploy-skill"],
  "recommendations": ["Narrow trigger to exclude deploy contexts", "Add prompt injection resistance section"]
}
```

`static_ceiling_hit` lists categories that cannot be fully scored without dynamic evidence, providing the user a clear rationale in Window 2.

---

## Key Design Decisions

1. **Sub-agents are fully self-contained** — they receive all rubric files, scenarios, and configuration in their initial prompt. They never block waiting for user input.

2. **User interaction is bounded to three windows** — configure upfront, approve dynamic testing between phases, receive report. No mid-execution interruptions.

3. **Gate questions in separate category files** — category rubrics evolve independently without touching the core dispatcher logic.

4. **Dynamic testing uses both pre-defined and generated scenarios** — pre-defined scenarios provide a consistent baseline; reviewer-generated scenarios target what static analysis revealed about this specific skill's risk profile.

5. **Static ceiling makes dynamic triggers automatic** — any category that hits its ceiling at ≤7 in static analysis is flagged for dynamic review. The user approves, not the reviewer.

6. **Parallel mode is default; single-agent is a resource-constrained fallback** — both modes produce identical output format.
