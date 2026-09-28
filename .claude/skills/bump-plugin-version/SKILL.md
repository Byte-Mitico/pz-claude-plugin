---
name: bump-plugin-version
description: Bump the pz-modding plugin version in .claude-plugin/plugin.json. Use for every new commit in this repo, before committing, so each commit ships with a new version.
---

# Bump plugin version

Every commit to this repo must include a version bump in `.claude-plugin/plugin.json` (the `version` field is the only place the version lives; `marketplace.json` does not carry one).

## Steps

1. Read the current `version` from `.claude-plugin/plugin.json`.
2. Look at the staged changes (`git diff --cached`), or the working tree if nothing is staged, and pick the bump:
   - **patch** (`0.1.0` -> `0.1.1`): fixes, wording or fact corrections, doc-only changes, manifest/metadata tweaks.
   - **minor** (`0.1.0` -> `0.2.0`): a new skill, command, agent or hook, or a substantial addition to an existing skill.
   - **major** (`0.1.0` -> `1.0.0`): breaking changes, such as renaming or removing a skill or command. Confirm with the user before a major bump.
3. Edit only the `version` value with the Edit tool, keeping the file's formatting.
4. Stage `.claude-plugin/plugin.json` together with the rest of the commit. Do not make a separate commit for the bump.
5. Tell the user the old and new version and the reason for the bump level.

## Rules

- Bump exactly once per commit; if the version was already changed in the working tree since `HEAD`, do not bump again.
- If the commit only touches files outside the plugin content (for example `.claude/` or `CLAUDE.md`), skip the bump and say so.
- The version must be plain `MAJOR.MINOR.PATCH` semver.
