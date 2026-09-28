# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A Claude Code plugin that helps developers build Project Zomboid mods. The repository is in its initial stage: there is no code, build system or test setup yet. Update this file as those are added.

## Plugin structure

Follow the standard Claude Code plugin layout:

- `.claude-plugin/plugin.json`: the plugin manifest (name, version, description). It is the only file inside `.claude-plugin/`.
- Component directories go at the repo root, not inside `.claude-plugin/`: `skills/` (each skill is `skills/<name>/SKILL.md`), `commands/`, `agents/`, `hooks/hooks.json`, and `.mcp.json` for MCP servers.

To test the plugin locally, load it with `claude --plugin-dir .` from the repo root.
