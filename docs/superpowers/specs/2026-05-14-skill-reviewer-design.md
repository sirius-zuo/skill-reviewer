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
1. **Header** — skill set name, review date, overall risk level, aggregate score (computed over applicable categories only)
2. **Dashboard** — traffic-light table: all skills × all 13 categories, color-coded (green ≥8, amber 6–7, red ≤5, grey = N/A)
3. **Per-skill sections** — collapsible; scorecard (applicable categories only, N/A flagged) + per-category findings with gate-level detail; prioritized recommendations; dynamic test results appended if run
4. **Rollup summary** — cross-skill patterns, Critical and Important recommendations across all skills, overall risk level with derivation rationale

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

### Category Applicability

Before scoring, the reviewer determines which categories apply to the skill being reviewed. Non-applicable categories are marked N/A, excluded from the average, and shown as grey in the report.

**Applicability rules:**

| Category | Applies when... | Always? |
|---|---|---|
| Skill Definition & Scope | — | Yes |
| Trigger & Invocation Design | — | Yes |
| Prompt / Instruction Quality | — | Yes |
| Decision Logic & Workflow | Skill has branching, loops, or multi-step logic | No |
| Tool Integration & Dependencies | Skill makes external tool calls | No |
| Skill Composability | Skill invokes or is designed to be invoked by other skills | No |
| Context & Memory Management | Skill spans multiple turns or maintains state | No |
| Test Coverage & Methodology | — | Yes (absence is a finding, not N/A) |
| Proven Reliability | — | Yes (absence is a finding, not N/A) |
| Safety & Security | — | Yes |
| Output Quality & Usability | — | Yes |
| Performance, Cost & Efficiency | Skill runs loops or makes multiple LLM calls | No |
| Autonomy Boundaries & Human Handoff | Skill takes actions with real-world consequences | No |

### Gate Structure

Each category rubric file defines three layers:

**Hard Blockers** — binary flags that floor the score at ≤3 regardless of other positives.
Example: trigger description absent, no safety boundary of any kind.

**Critical Gates** — 3 yes/no questions, each worth 2 points (max 6).
Things that must be present for the skill to be minimally trustworthy.

**Quality Gates** — 4 yes/no questions, each worth 1 point (max 4).
Things that separate acceptable from excellent.

**Score = min(critical_gates × 2 + quality_gates, 10), capped at 3 by hard blockers.**

Each gate answer includes a one-line justification. This makes scores reproducible across runs.

**Overall score = average over applicable categories only.**

### Risk Level

The conclusion of each skill review is a **risk level**, not a binary approve/reject. Risk level is derived from scores across applicable categories, with critical categories (Safety, Scope, Trigger & Invocation) weighted more heavily.

| Risk Level | Derivation |
|---|---|
| **Low** | All applicable categories ≥ 8; Safety ≥ 9 |
| **Medium** | 1–2 non-critical categories score 6 or 7; no critical category below 8 |
| **High** | Any critical category (Safety, Scope, Trigger) scores 6–7, OR 3+ applicable categories below 7, OR any non-safety/scope hard blocker triggered |
| **Critical** | Safety or Scope hard blocker triggered, OR Safety < 6, OR Scope < 6 |

Risk level is accompanied by a one-paragraph rationale explaining which scores drove it and what would need to change to lower it.

### Recommendations

Each recommendation is:
- **Linked to the specific gate that failed** — not "improve safety" but "add prompt injection resistance: skill passes user input to tools without sanitization (Safety, Critical Gate 2)"
- **Priority-tiered:**
  - 🔴 **Critical** — triggers a hard blocker or drives a High/Critical risk level; must be resolved before use
  - 🟡 **Important** — caps score, prevents reaching Low risk; should be resolved
  - 🟢 **Suggested** — would move a 7 to 8 or 9; nice to have
- **Actionable** — each states exactly what to add, change, or remove

The risk level summary in the report links directly to all Critical and Important recommendations, so a skill author knows precisely what to fix to lower their risk level.

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
  "applicable_categories": ["scope", "trigger_invocation", "prompt_quality", "safety_security", "output_quality", "test_coverage", "proven_reliability"],
  "na_categories": ["decision_logic", "tool_integration", "composability", "context_memory", "performance_cost", "autonomy_boundaries"],
  "static_scores": {
    "scope":              { "score": 8, "blockers_triggered": [], "gates": {}, "issues": [] },
    "trigger_invocation": { "score": 6, "blockers_triggered": [], "gates": {}, "issues": ["Conflicts with deploy-skill trigger"] },
    "safety_security":    { "score": 6, "blockers_triggered": [], "gates": {}, "issues": ["No prompt injection resistance documented"] }
  },
  "dynamic_scores": null,
  "static_ceiling_hit": ["safety_security", "proven_reliability"],
  "dynamic_recommended": true,
  "overall_score": 7.1,
  "risk_level": "high",
  "risk_rationale": "Safety scores 6 — below the critical category threshold of 8, triggering High risk. Trigger & Invocation scores 6, also below threshold. Resolving the Safety gate failures would move this to Medium risk.",
  "recommendations": [
    {
      "priority": "critical",
      "category": "safety_security",
      "gate": "Critical Gate 2",
      "text": "Add prompt injection resistance: skill passes user input to tools without sanitization"
    },
    {
      "priority": "important",
      "category": "trigger_invocation",
      "gate": "Quality Gate 2",
      "text": "Narrow trigger description to exclude deploy contexts — currently conflicts with deploy-skill trigger"
    }
  ]
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
