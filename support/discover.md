# Skill Discovery Instructions

You are performing the discovery phase of a skill review. Your job is to scan a directory and produce a manifest of all skills found.

## Input Validation

Before scanning, validate the path argument:

**Local path:**
- Verify the path exists and is a readable directory.
- Reject paths containing shell metacharacters: `;`, `|`, `&`, `$`, or backtick. If found, report: "The path `[value]` contains unsafe characters and cannot be scanned. Please provide a valid local path." and stop.
- If the path does not exist or is not readable, report: "The path `[value]` does not exist or is not readable." and stop.

**GitHub URL:**
- Verify the URL matches the pattern `https://github.com/<org>/<repo>` (with optional `.git` suffix).
- If the clone fails, report the error (exit code + message) and stop. Example: "Clone failed: repository not found at [URL]. Verify the URL and your git credentials." Do not attempt discovery on a partially-cloned directory.

**Neither:**
- If the value is neither a valid local path nor a valid GitHub URL, report: "The path `[value]` is not a valid local path or GitHub URL. Please provide a valid path." and stop.

## What counts as a skill

A file is a skill if it meets ANY of these criteria:
- Named `SKILL.md` (case-insensitive)
- Named `skill.md`, `AGENT.md`, or `agent.md`
- Any `.md` file with YAML frontmatter containing BOTH a `name:` field AND a `description:` field

A file with only one of `name:` or `description:` does NOT qualify — both are required.

A directory is a sub-skill if it contains its own skill file (by the rules above).

## What counts as a supporting artifact

Any non-skill file that lives in a skill's directory:
- Instruction files (`.md` without qualifying frontmatter)
- Scripts (`.sh`, `.py`, `.js`, `.ts`, etc.)
- Templates (`.html`, `.json`, `.yaml`)
- Example and scenario files

Supporting artifacts are catalogued under their parent skill and included in that skill's review context.

## Directory scanning rules

1. Start from the root directory provided.
2. **Skip all invisible files and directories** — any file or folder whose name begins with `.` (e.g., `.git`, `.github`, `.claude`, `.DS_Store`, `.cursor`). Never recurse into them.
3. **Skip infrastructure directories** — any directory whose name (case-insensitive) exactly matches one of: `tests`, `test`, `fixtures`, `fixture`, `examples`, `example`, `support`, `share`, `scenarios`, `scenario`, `samples`, `sample`, `docs`, `doc`. These directories are supporting infrastructure for a skill project, not deployable skills. Skip the entire subtree — do not recurse into them or catalogue anything inside as a skill or artifact.
4. For each `.md` file found, check if it qualifies as a skill.
5. For each remaining visible subdirectory, recurse and apply the same rules.
6. A subdirectory whose skill file is a DIFFERENT skill from the parent = sub-skill relationship.
7. If a GitHub URL was provided instead of a local path, clone it to a temp directory first: `git clone <url> /tmp/skill-review-<timestamp>`, then scan from there.

## README handling

If a `README.md` file exists at the root and does NOT qualify as a skill file (no `name:` frontmatter), include its content as `rollup_context` in the manifest.

## Output format

Always resolve `root_path` to an absolute canonical path before writing the manifest (expand `~`, resolve symlinks, resolve relative paths from the working directory).

Produce a JSON manifest:

```json
{
  "root_path": "/absolute/canonical/path/to/reviewed/dir",
  "self_review": false,
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
- **Self-review guard:** After generating the manifest, compare the absolute `root_path` to the absolute path of the skill-review's installation directory (the parent of the `support/` directory containing this file — the main agent knows this from where it found `support/discover.md`). If they are the same, emit a warning:
  > "Warning: the provided path appears to be the skill-review itself. Reviewing a skill against its own rubric may produce unreliable results. Do you want to proceed? [yes / no]"
  If the user answers no, stop. If yes, proceed and set `"self_review": true` in the manifest (default is `false`).
