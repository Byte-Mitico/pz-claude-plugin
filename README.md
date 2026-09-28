# pz-modding: Claude Code plugin for Project Zomboid mods

Skills and commands for building **Build 42** Project Zomboid mods. Content is derived from the vanilla Lua, the decompiled Java, and the [pz-modding-guide](https://github.com/gotmayonase/pz-modding-guide).

## Skills

| Skill | Covers |
|---|---|
| `pz-source-lookup` | Where to verify APIs, events, IDs in vanilla Lua/Java/scripts |
| `pz-mod-structure` | Folder layout, `mod.info`, `workshop.txt`, modules, translations, B41 migration |
| `pz-item-scripts` | `item` blocks, `ItemType`, container properties |
| `pz-craft-recipes` | `craftRecipe` syntax, tags, skill IDs |
| `pz-lua-events` | `Events.*`, verified event arguments |
| `pz-lua-api` | Lua-callable Java classes and common calls |
| `pz-multiplayer` | client/server/shared, command pattern |
| `pz-timed-actions-ui` | Timed actions, context menus, ISUI |

## Commands

- `/pz-new-mod <ModId> [dir]`: scaffold a mod.

## Try it

```
claude --plugin-dir .
```

## Source paths

`pz-source-lookup` lists the default local paths for the game's Lua, scripts and decompiled Java. Adjust that skill if your paths differ.
