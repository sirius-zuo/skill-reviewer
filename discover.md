# Skill Discovery Instructions

You are performing the discovery phase of a skill review. Your job is to scan a directory and produce a manifest of all skills found.

## What counts as a skill

A file is a skill if it meets ANY of these criteria:
- Named `SKILL.md` (any case)
- Named `index.md` AND contains YAML frontmatter with a `name:` field
- Any `.md` file with YAML frontmatter containing a `name:` field

A directory is a sub-skill if it contains its own skill file (by the rules above).

## What counts as a supporting artifact

Any file that is NOT a skill file but lives in a skill's directory:
- Scripts (`.sh`, `.py`, `.js`, etc.)
- HTML templates (`.html`)
- Scenario files (`.md` files without `name:` frontmatter)
- Example files

Supporting artifacts are catalogued under their parent skill and included in that skill's review context.

## Directory scanning rules

1. Start from the root directory provided.
2. For each `.md` file found, check if it qualifies as a skill (frontmatter with `name:`).
3. For each subdirectory, recurse and apply the same rules.
4. A subdirectory whose skill file is a DIFFERENT skill from the parent = sub-skill relationship.
5. If a GitHub URL was provided instead of a local path, clone it to a temp directory first: `git clone <url> /tmp/skill-review-<timestamp>`, then scan from there.

## README handling

If a `README.md` file exists at the root and does NOT qualify as a skill file (no `name:` frontmatter), include its content as `rollup_context` in the manifest.

## Output format

Produce a JSON manifest:

```json
{
  "root_path": "/path/to/reviewed/dir",
  "rollup_context": "README content if present, else null",
  "skills": [
    {
      "skill_name": "name: field from frontmatter; if absent, filename without .md extension",
      "skill_path": "relative/path/to/SKILL.md",
      "type": "main_skill | sub_skill | supporting_tool",
      "parent": "parent skill name if sub_skill, else null",
      "supporting_artifacts": ["relative/path/to/file.sh", "..."],
      "all_files": ["all files belonging to this skill unit"]
    }
  ]
}
```

## Edge cases

- If NO skills are found: output `{ "skills": [], "error": "No skill files found in directory" }` and stop.
- If a file has frontmatter but no `name:` field: treat as supporting artifact.
- If two skills share the same `name`: flag as a conflict in the manifest with `"name_conflict": true`.
