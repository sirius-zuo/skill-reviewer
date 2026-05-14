---
name: skill-reviewer
description: Use when the user asks to review an agent skill, audit a skill directory, check skill quality, or evaluate a skillset. Accepts a local directory path or GitHub repo URL. Do NOT use for reviewing code, documentation, or non-skill files.
---

# Skill Reviewer

Reviews agent skills and produces an HTML report with per-skill risk levels, category scores, and prioritized recommendations.

## Invocation

Arguments:
- **path** (required): Local directory path or GitHub repo URL containing the skill(s) to review.
- **mode** (optional): `parallel` (default) or `single`. Use `single` in resource-constrained environments.

## Phase 1 — Discovery

Read and follow the instructions in `discover.md` (in the same directory as this SKILL.md).

Apply the discovery rules to the provided path. If a GitHub URL was provided, clone it first.

Output: a skill manifest JSON object.

If the manifest contains `"error"` (no skills found), report the error to the user and stop.

## Phase 2 — Configuration (User Interaction Window 1)

Ask the user the following questions before proceeding. Present all questions together in one message:

1. **Mode:** "Parallel mode spawns one sub-agent per skill simultaneously (faster, higher resource use). Single mode reviews sequentially (slower, lower resource use). Which do you prefer? [parallel / single, default: parallel]"

2. **Output path:** "Where should the HTML report be saved? [default: docs/review/ inside the reviewed skill's root]"

3. **Category exclusions:** "Are there any review categories you want to skip? Options: decision-logic, tool-integration, composability, context-memory, performance-cost, autonomy-boundaries. [default: none]"

4. **Dynamic testing preference:** "When a category hits its static score ceiling (≤7), should I automatically trigger dynamic testing, or ask you first? [auto / ask, default: ask]"

Wait for user responses. Record the answers as configuration. These are passed to all sub-agents.

## Phase 3 — Static Analysis

For each skill in the manifest:

**If mode = parallel:**
Spawn one sub-agent per skill simultaneously using the Agent tool. Each sub-agent receives:
- The skill's files (all_files from the manifest)
- The full content of `static-review.md`
- The full content of all 13 category rubric files from `categories/`
- The configuration from Phase 2
- Instruction: "Review this skill statically and return the JSON result described in static-review.md."

**If mode = single:**
Review each skill in sequence within this session, following the steps in `static-review.md` for each skill.

Collect all JSON results. If any sub-agent fails to return valid JSON, note the error and continue with remaining skills.

## Phase 4 — Dynamic Testing Gate (User Interaction Window 2)

After all static results are collected:

1. Identify skills where `dynamic_recommended = true`.
2. If none: skip to Phase 6.
3. If any: present a summary to the user:

> "Static review complete. Dynamic testing is recommended for:
> [for each skill with dynamic_recommended=true:]
> - **[skill_name]** — [list flagged categories with scores]
>   Reason: [static_ceiling_hit categories]
>
> Proceed with dynamic testing on: [all / select specific skills / skip]?"

If configuration from Phase 2 was `dynamic: auto`, skip this prompt and proceed with all recommended skills automatically.

Wait for user response if asking.

## Phase 5 — Dynamic Testing

For each approved skill:

Spawn a sub-agent (or run in-session if mode=single) with:
- The skill's files
- The static review JSON result for this skill
- The full content of `dynamic-review.md`
- Relevant scenario files from `scenarios/` (per the mapping in dynamic-review.md)
- Configuration from Phase 2

Instruction: "Run dynamic testing on this skill using the JSON result and scenario files. Return the updated JSON."

Collect updated JSON results.

## Phase 6 — Report Generation

Read `report.md` and follow its assembly instructions to produce the HTML report.

Use all collected JSON results (static + dynamic where available).

Save the report to the path specified in Phase 2 configuration.

## Phase 7 — Report Delivery (User Interaction Window 3)

Print to the terminal:

```
Skill review complete.
Report saved to: [absolute path]

Summary:
- Skills reviewed: [count]
- Overall risk level: [level]
- Average score: [score]/10
- Critical issues: [count]
- Important issues: [count]

Open the HTML report for full details and per-skill breakdowns.
```

Ask: "Would you like a terminal summary of the top issues per skill?"

If yes: print the top 3 recommendations (by priority) for each skill.
