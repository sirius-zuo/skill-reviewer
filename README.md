# Skill Review

A portable AI agent skill that reviews other agent skills and produces a structured HTML report. Point it at a local directory or GitHub repo — it discovers all skills, scores them across 13 categories, identifies risks, and delivers prioritized recommendations.

Works with any AI coding agent that can read markdown instruction files: Claude Code, Codex, Cursor, Windsurf, GitHub Copilot, and others.

---

## What It Does

- **Discovers** skills automatically — handles a single skill, a skill with supporting files, or a full skillset with sub-skills
- **Scores** each skill across 13 review categories using a gate-based rubric (Hard Blockers → Critical Gates → Quality Gates)
- **Skips** categories that don't apply (e.g., Tool Integration is skipped for skills that make no tool calls)
- **Offers dynamic testing** for categories where static analysis alone can't give a full score
- **Produces** a self-contained HTML report with a traffic-light dashboard, per-skill findings, and a cross-skill rollup summary
- **Concludes** with a risk level per skill: **Low / Medium / High / Critical** — not a binary approve/reject

---

## Installation

The skill is a directory of markdown instruction files. Installation means making those files accessible to your AI agent.

### Claude Code

Claude Code supports native slash commands for skills. The `name: skill-review` frontmatter in `SKILL.md` registers it automatically.

```bash
git clone https://github.com/sirius-zuo/skill-review.git
cp -r skill-review ~/.claude/skills/skill-review
```

**Invoke with the slash command:**

```
/skill-review ./my-skill
/skill-review https://github.com/org/agent-skills
```

**Or via natural language** — Claude recognises the description in `SKILL.md` and loads the skill automatically:

```
Review the skill at ./my-skill
Audit the skillset at ./agent-skills/
```

**To make it available across all projects**, reference it in your global `~/.claude/CLAUDE.md`:

```markdown
When asked to review a skill or skillset, use the skill at
~/.claude/skills/skill-review/SKILL.md.
```

### Codex

Codex reads `AGENTS.md` at startup. Add a reference so the agent knows the skill exists:

```bash
git clone https://github.com/sirius-zuo/skill-review.git ~/skills/skill-review
```

In your project's `AGENTS.md`:

```markdown
## Available Skills

**skill-review** — reviews agent skills and produces an HTML report.
Instructions: ~/skills/skill-review/SKILL.md
To use: ask Codex to review a skill directory or GitHub repo.
```

**Invoke via natural language:**

```
Review the skill at ./my-skill
Audit the skillset at https://github.com/org/agent-skills
```

Codex will read `AGENTS.md`, find the skill reference, load `SKILL.md`, and follow its instructions.

### Cursor

Cursor picks up workspace rules from `.cursor/rules/`. Create a rule file that registers the skill:

```bash
git clone https://github.com/sirius-zuo/skill-review.git ~/skills/skill-review
mkdir -p .cursor/rules
```

Create `.cursor/rules/skill-review.md`:

```markdown
---
description: Use when the user asks to review an agent skill or skillset
---

To review a skill, read and follow the instructions in:
~/skills/skill-review/SKILL.md
```

**Invoke in Cursor's chat:**

```
Review the skill at ./my-skill
@skill-review audit ./agent-skills/
```

Cursor matches the rule's `description` field to your request and activates it.

### Windsurf

Windsurf's Cascade reads `.windsurfrules` on startup. Append the skill reference:

```bash
git clone https://github.com/sirius-zuo/skill-review.git ~/skills/skill-review
```

In `.windsurfrules`:

```
When asked to review an agent skill or skillset, read and follow the
instructions in ~/skills/skill-review/SKILL.md.
```

**Invoke via natural language in Cascade:**

```
Review the skill at ./my-skill
Audit the skillset at ./agent-skills/
```

### GitHub Copilot

Copilot Chat picks up custom instructions from `.github/copilot-instructions.md`:

```bash
git clone https://github.com/sirius-zuo/skill-review.git ~/skills/skill-review
```

In `.github/copilot-instructions.md`:

```markdown
## Skill Review

When asked to review an agent skill or skillset, read and follow the
instructions in ~/skills/skill-review/SKILL.md.
```

**Invoke in Copilot Chat:**

```
Review the skill at ./my-skill
```

For a one-off review without any config, point Copilot directly at the skill file:

```
Read ~/skills/skill-review/SKILL.md and review the skill at ./my-skill
```

### Any Other Agent

The skill is plain markdown. Any agent that can read files and follow instructions works:

```
Read ~/skills/skill-review/SKILL.md and follow its instructions
to review the skill at ./path/to/skill
```

---

## Usage

### Invocation summary

| Platform | How to invoke |
|----------|--------------|
| Claude Code | `/skill-review ./my-skill` |
| Codex | `Review the skill at ./my-skill` |
| Cursor | `Review the skill at ./my-skill` |
| Windsurf | `Review the skill at ./my-skill` |
| GitHub Copilot | `Review the skill at ./my-skill` |

Claude Code registers `/skill-review` automatically from the `name:` field in `SKILL.md`. On all other platforms the agent picks up the skill from its rules or instructions config and activates it when you describe what you want.

### What happens after invocation

The agent will:

1. **Discover** all skills in the directory
2. **Ask four questions** upfront (mode, output path, category exclusions, dynamic testing preference)
3. **Run static analysis** on each skill
4. **Offer dynamic testing** for any category that hit its scoring ceiling
5. **Save an HTML report** and print a terminal summary

### Configuration questions

When you start a review, the agent asks:

| Question | Options | Default |
|----------|---------|---------|
| **Mode** | `parallel` — one sub-agent per skill, faster; `single` — sequential, lower resource use | `parallel` |
| **Output path** | Any local path | `docs/review/` inside the reviewed root |
| **Category exclusions** | Any of the 6 conditional categories | none |
| **Dynamic testing** | `auto` — trigger automatically; `ask` — prompt before running | `ask` |

---

## Example

**Reviewing a single skill** (Claude Code slash command):

```
User:   /skill-review ./deploy-skill

Agent:  Discovered 1 skill: deploy-skill

        Before I start, a few questions:
        1. Mode? [parallel / single, default: parallel]
        2. Output path? [default: ./deploy-skill/docs/review/]
        3. Categories to skip? [default: none]
        4. Dynamic testing: auto or ask? [default: ask]

User:   All defaults.

Agent:  Running static analysis on deploy-skill...

        Static review complete. Dynamic testing recommended for:
        - deploy-skill — Safety: 6, Proven Reliability: 5
          (these categories hit their static scoring ceiling)

        Proceed with dynamic testing on: all / select / skip?

User:   All.

Agent:  Running dynamic tests...

        Skill review complete.
        Report: ./deploy-skill/docs/review/skill-review-2026-05-14.html

        Summary:
        - Skills reviewed: 1
        - Overall risk level: High
        - Average score: 6.4/10
        - Critical issues: 1
        - Important issues: 3

        Would you like a terminal summary of the top issues?
```

**Reviewing a full skillset** (natural language, any platform):

```
User:   Review the skillset at ./agent-skills/

Agent:  Discovered 4 skills:
        - auth-skill      (skills/auth/SKILL.md)    — main_skill
        - deploy-skill    (skills/deploy/SKILL.md)  — main_skill
        - notify-skill    (skills/notify/SKILL.md)  — main_skill
        - shared-utils    (skills/shared/SKILL.md)  — supporting_tool

        [proceeds with configuration questions...]
```

**The HTML report includes:**

- A **dashboard** — all skills × all 13 categories, color-coded green (≥8) / amber (6–7) / red (≤5) / grey (N/A)
- **Per-skill sections** — collapsible, with gate-level findings and recommendations by priority
- A **rollup summary** — cross-skill patterns, top issues, overall risk level with rationale

---

## Review Categories

| # | Category | Applied When | Max Score |
|---|----------|-------------|-----------|
| 1 | Skill Definition & Scope | Always | 10 |
| 2 | Trigger & Invocation Design | Always | 8 |
| 3 | Prompt / Instruction Quality | Always | 10 |
| 4 | Decision Logic & Workflow | Skill has branching or loops | 8 |
| 5 | Tool Integration & Dependencies | Skill makes tool calls | 8 |
| 6 | Skill Composability | Skill invokes or is invoked by others | 8 |
| 7 | Context & Memory Management | Skill is multi-turn or stateful | 8 |
| 8 | Test Coverage & Methodology | Always (absence is a finding) | 8 |
| 9 | Proven Reliability | Always (absence is a finding) | 7 static, 10 with dynamic |
| 10 | Safety & Security | Always | 7 static, 10 with dynamic |
| 11 | Output Quality & Usability | Always | 10 |
| 12 | Performance, Cost & Efficiency | Skill runs loops or multi-LLM calls | 10 |
| 13 | Autonomy Boundaries & Human Handoff | Skill takes real-world actions | 8 |

Categories that don't apply are marked N/A and excluded from the score average.

---

## Scoring Model

Each category uses a three-tier gate structure:

### Hard Blockers
Binary flags that immediately cap the score at ≤ 3, regardless of how well the skill does elsewhere. A Hard Blocker means a fundamental requirement is missing — for example, no error handling at all, or credentials passed without redaction. If you see a 🔴 BLOCKER in the report, that category's score was capped here.

### Critical Gates (CG)
Three yes/no questions per category, worth **2 points each** (max 6 points). These cover the things a skill must get right to be minimally trustworthy — for example, "does this skill explicitly handle prompt injection?" Each answer includes a one-line justification so scores are reproducible.

### Quality Gates (QG)
Four yes/no questions per category, worth **1 point each** (max 4 points). These separate acceptable from excellent — things like whether examples are provided, whether output length is bounded, or whether failure modes are categorised. A skill can be usable without passing all QGs, but passing them moves the score from 6 toward 10.

### Formula

```
score = (CG_yes × 2) + (QG_yes × 1)   max = 10
```

Capped at 3 if a Hard Blocker fires. For example, a category where 1 CG passes and 0 QGs pass scores 2.

Some categories have a **static ceiling** below 10 (e.g. Safety caps at 7 statically) because the remaining points can only be earned through dynamic testing — actually running the skill against adversarial scenarios, not just reading its instructions.

## Risk Levels

| Level | Condition |
|-------|-----------|
| **Low** | All applicable categories ≥ 8; Safety ≥ 9 |
| **Medium** | 1–2 non-critical categories score 6 or 7; no critical category below 8 |
| **High** | Any critical category (Safety, Scope, Trigger) scores 6–7, OR 3+ categories below 7 |
| **Critical** | Safety or Scope hard blocker triggered, OR Safety < 6, OR Scope < 6 |

---

## File Structure

```
skill-review/
  SKILL.md                      # main dispatcher — start here
  support/
    discover.md                 # skill discovery instructions
    static-review.md            # static analysis instructions
    dynamic-review.md           # dynamic testing instructions
    report.md                   # HTML report assembly instructions
    report-template.html        # self-contained HTML/CSS template
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
