---
name: pz-lua-events
description: Project Zomboid Lua event system (Events.OnX.Add), event names and arguments as triggered by the game's Java, and performance rules. Use when hooking game behavior from Lua.
---

# Lua events

```lua
local function onEquip(character, item) ... end
Events.OnEquipPrimary.Add(onEquip)
Events.OnEquipPrimary.Remove(onEquip)
```

Lua files under `media/lua/{client,server,shared}` load automatically. Use `require "Path/File"` (relative to the lua subfolder) for load-order dependencies, as vanilla does (`require "TimedActions/ISBaseTimedAction"`).

## Events verified in the decompiled Java (`LuaEventManager.java`, `triggerEvent` call sites)

| Event | Arguments | Notes |
|---|---|---|
| `OnGameBoot` | none | Very early, before world. Also on server. |
| `OnGameStart` | none | Client world start. |
| `OnNewGame` | `player, square` | New character created. |
| `OnCreatePlayer` | `playerIndex, player` | Also fires for splitscreen players. |
| `OnPlayerUpdate` | `player` | Every tick. Client: local players. Server: every player. |
| `OnPlayerDeath` / `OnCharacterDeath` | `player` / `character` | |
| `OnZombieDead` | `zombie` | |
| `OnHitZombie` | `zombie, wielder, bodyPart, weapon` | |
| `OnWeaponHitCharacter` | `wielder, target, weapon, damage` | |
| `OnEquipPrimary` | `character, item` | Secondary: `OnEquipSecondary`. |
| `OnContainerUpdate` | varies (often none) | Do not assume arguments. |
| `OnFillContainer` | `roomName, containerType, container` | Loot generated into a container. |
| `OnFillInventoryObjectContextMenu` | `playerNum, context, items` | Inventory right-click. |
| `OnFillWorldObjectContextMenu` | `playerNum, context, worldObjects, test` | World right-click. |
| `OnKeyPressed` | `keyCode` | |
| `OnTick` | `tickNumber` | |
| `EveryOneMinute`, `EveryTenMinutes`, `EveryHours`, `EveryDays` | none | Game time. |
| `OnClientCommand` | `module, command, player, args` | Server side. |
| `OnServerCommand` | `module, command, args` | Client side. |
| `OnInitGlobalModData` | `isNewGame` | Init ModData here. |
| `OnObjectAdded`, `OnTileRemoved`, `LoadGridsquare` | varies | World changes. |

The full list is ~290 events. Before using one not listed, read its `triggerEvent("Name", ...)` call sites with grep in `zombie/` to confirm arguments. Do not invent event names; a typo yields `Events.X == nil` and an error at load.

## Rules

- `OnPlayerUpdate`, `OnTick` and `OnRenderTick` run every frame: guard `nil` and exit early, avoid allocating tables, cache at file scope.
- Prefer coarse events (`EveryOneMinute`, `EveryHours`) for periodic work.
- Keep state in `ModData` (`ModData.getOrCreate("MyMod")`, `ModData.transmit`) rather than Lua globals when it must persist or sync.
- Namespace everything (`MyMod = MyMod or {}`); vanilla globals are plentiful.
