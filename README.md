# Skill Reviewer

A Claude Code skill that reviews agent skills and produces a structured HTML report. Point it at a local directory or GitHub repo — it discovers all skills, scores them across 13 categories, flags risks, and delivers a prioritized set of recommendations.

## What It Does

- **Discovers** skills automatically — handles a single skill, a skill with supporting files, or a full skillset with sub-skills
- **Scores** each skill across 13 review categories using a gate-based rubric (Hard Blockers → Critical Gates → Quality Gates)
- **Skips** categories that don't apply (e.g., Tool Integration is skipped for skills that make no tool calls)
- **Flags** categories where a static review can't give a full score — and offers targeted dynamic testing for those
- **Produces** a self-contained HTML report with a traffic-light dashboard, per-skill findings, and a cross-skill rollup summary
- **Concludes** with a risk level per skill: Low / Medium / High / Critical — not a binary approve/reject

## Requirements

- [Claude Code](https://claude.ai/code) (CLI, desktop app, or IDE extension)
- The [Superpowers plugin](https://github.com/anthropics/claude-code) installed in Claude Code (provides the `Skill` tool infrastructure)

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/your-org/skill-reviewer.git
```

### 2. Add to your Superpowers plugin

Place the `skill-reviewer/` directory inside your Superpowers skills folder:

```bash
cp -r skill-reviewer ~/.claude/plugins/superpowers/skills/skill-reviewer
```

Or, if you maintain your own plugin:

```
your-plugin/
  skills/
    skill-reviewer/        ← drop the entire repo here
      SKILL.md
      support/
      categories/
      scenarios/
```

### 3. Verify Claude Code can see it

In a Claude Code session, ask:

```
/skill-reviewer
```

Claude should acknowledge the skill and prompt for a path to review. If it doesn't, check that `SKILL.md` is at the root of the skill directory with the correct frontmatter (`name: skill-reviewer`).

## Usage

Invoke the skill by asking Claude to review a skill, or use the slash command:

```
/skill-reviewer path/to/my-skill
```

```
/skill-reviewer https://github.com/org/repo
```

Claude will:

1. **Discover** all skills in the directory
2. **Ask four questions** upfront (mode, output path, category exclusions, dynamic testing preference)
3. **Run static analysis** on each skill
4. **Offer dynamic testing** for any category that hit its scoring ceiling
5. **Save an HTML report** and print a terminal summary

### Options

| Option | Values | Default |
|--------|--------|---------|
| Mode | `parallel` (one sub-agent per skill, faster) or `single` (sequential, lower resource use) | `parallel` |
| Output path | Any local path | `docs/review/` inside the reviewed root |
| Category exclusions | Any of the 6 conditional categories | none |
| Dynamic testing | `auto` (trigger automatically) or `ask` (prompt before running) | `ask` |

## Example

**Reviewing a single skill:**

```
User: /skill-reviewer ~/projects/my-skills/deploy-skill

Claude: Discovered 1 skill: deploy-skill (SKILL.md)

Before I start, a few questions:
1. Mode: parallel or single? [default: parallel]
2. Output path: [default: ~/projects/my-skills/deploy-skill/docs/review/]
3. Categories to skip? [default: none]
4. Dynamic testing: auto or ask? [default: ask]

User: parallel, defaults, none, ask

Claude: Running static analysis on deploy-skill...

Static review complete. Dynamic testing recommended for:
- deploy-skill — Safety: 6, Proven Reliability: 5
  Reason: safety_security and proven_reliability hit their static ceiling

Proceed with dynamic testing on: all / select / skip?

User: all

Claude: Running dynamic testing...

Skill review complete.
Report saved to: ~/projects/my-skills/deploy-skill/docs/review/skill-review-2026-05-14.html

Summary:
- Skills reviewed: 1
- Overall risk level: High
- Average score: 6.4/10
- Critical issues: 1
- Important issues: 3
```

**Reviewing a full skillset:**

```
User: /skill-reviewer ~/projects/agent-skills/

Claude: Discovered 4 skills:
- auth-skill (skills/auth/SKILL.md) — main_skill
- deploy-skill (skills/deploy/SKILL.md) — main_skill
- notify-skill (skills/notify/SKILL.md) — main_skill
- shared-utils (skills/shared/SKILL.md) — supporting_tool

...
```

**The HTML report includes:**

- A dashboard table — all skills × all 13 categories, color-coded green / amber / red / grey
- Per-skill sections — collapsible, with gate-level findings and prioritized recommendations
- A rollup summary — cross-skill patterns, top issues, overall risk level with rationale

## Review Categories

| # | Category | Always Applied | Static Ceiling |
|---|----------|---------------|----------------|
| 1 | Skill Definition & Scope | Yes | 10 |
| 2 | Trigger & Invocation Design | Yes | 8 |
| 3 | Prompt / Instruction Quality | Yes | 10 |
| 4 | Decision Logic & Workflow | Only if skill has branching/loops | 8 |
| 5 | Tool Integration & Dependencies | Only if skill makes tool calls | 8 |
| 6 | Skill Composability | Only if skill invokes/is invoked by others | 8 |
| 7 | Context & Memory Management | Only if skill is multi-turn/stateful | 8 |
| 8 | Test Coverage & Methodology | Yes (absence is a finding) | 8 |
| 9 | Proven Reliability | Yes (absence is a finding) | 7 → 10 with dynamic |
| 10 | Safety & Security | Yes | 7 → 10 with dynamic |
| 11 | Output Quality & Usability | Yes | 10 |
| 12 | Performance, Cost & Efficiency | Only if skill runs loops/multi-LLM | 10 |
| 13 | Autonomy Boundaries & Human Handoff | Only if skill takes real-world actions | 8 |

Categories that don't apply are marked N/A and excluded from the average score.

## Scoring Model

Each category uses a three-tier gate structure:

- **Hard Blockers** — binary flags that cap the score at ≤ 3 regardless of other gates
- **Critical Gates** — 3 yes/no questions worth 2 points each (max 6 points)
- **Quality Gates** — 4 yes/no questions worth 1 point each (max 4 points)

`score = min(critical_gates × 2 + quality_gates, 10)`, capped at 3 if any Hard Blocker fires.

Every gate answer includes a one-line justification — scores are reproducible across runs.

## Risk Levels

| Level | Condition |
|-------|-----------|
| **Low** | All applicable categories ≥ 8; Safety ≥ 9 |
| **Medium** | 1–2 non-critical categories score 6 or 7; no critical category below 8 |
| **High** | Any critical category (Safety, Scope, Trigger) scores 6–7, OR 3+ categories below 7 |
| **Critical** | Safety or Scope hard blocker triggered, OR Safety < 6, OR Scope < 6 |

## File Structure

```
skill-reviewer/
  SKILL.md                      # main dispatcher
  support/
    discover.md                 # skill discovery instructions
    static-review.md            # static analysis sub-agent instructions
    dynamic-review.md           # dynamic testing sub-agent instructions
    report.md                   # HTML report assembly instructions
    report-template.html        # self-contained HTML/CSS template
  categories/
    01-scope.md … 13-autonomy-boundaries.md
  scenarios/
    prompt-injection.md
    edge-cases.md
    adversarial-tools.md
    tool-failure.md
    context-overflow.md
```
