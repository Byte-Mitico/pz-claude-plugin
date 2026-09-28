# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A Claude Code plugin (`pz-modding`) that helps developers build Project Zomboid (Build 42) mods. It is content only (Markdown skills and commands, no build system or tests). Skill content is derived from the vanilla Lua, the decompiled Java and the pz-modding-guide; see `skills/pz-source-lookup/SKILL.md` for the source paths. Verify facts against those sources before adding them to a skill.

Current components: skills `pz-source-lookup`, `pz-mod-structure`, `pz-item-scripts`, `pz-craft-recipes`, `pz-lua-events`, `pz-lua-api`, `pz-multiplayer`, `pz-timed-actions-ui`, `pz-controller-input`; command `/pz-new-mod`.

## Plugin structure

Follow the standard Claude Code plugin layout:

- `.claude-plugin/plugin.json`: the plugin manifest (name, version, description). It is the only file inside `.claude-plugin/`.
- Component directories go at the repo root, not inside `.claude-plugin/`: `skills/` (each skill is `skills/<name>/SKILL.md`), `commands/`, `agents/`, `hooks/hooks.json`, and `.mcp.json` for MCP servers.

To test the plugin locally, load it with `claude --plugin-dir .` from the repo root.

## Versioning

For each new commit, run the project skill `bump-plugin-version` (`.claude/skills/bump-plugin-version/SKILL.md`) before committing, so the `version` in `.claude-plugin/plugin.json` is updated and staged with the commit.
