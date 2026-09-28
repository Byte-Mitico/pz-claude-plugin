---
name: pz-multiplayer
description: Project Zomboid client/server/shared Lua contexts and the sendClientCommand / sendServerCommand pattern for multiplayer-safe mods. Use when Lua changes game state, adds commands, or must work in MP or on a listen server.
---

# Multiplayer architecture

| Folder | Runs on | Use for |
|---|---|---|
| `lua/client/` | each player's machine | detection, UI, sending commands |
| `lua/server/` | dedicated server / listen-server host | authoritative state changes |
| `lua/shared/` | both | constants, utilities, data tables |

On a listen server the host runs both client and server Lua. Prefer file placement over `isClient()` / `isServer()` guards (`isMultiplayer()` also exists).

The engine already syncs container contents, script properties (`RunSpeedModifier`, `RequiresEquippedBothHands`), pickup/drop and crafting. Only custom Lua that changes state needs the pattern below.

## Command pattern

Client detects, server validates and applies:

```lua
-- client
sendClientCommand(player, "MyMod", "doThing", { itemId = item:getID() })

-- server
local function onClientCommand(module, command, player, args)
    if module ~= "MyMod" then return end
    if command == "doThing" then
        -- re-validate everything; never trust the client
    end
end
Events.OnClientCommand.Add(onClientCommand)
```

Server to client:

```lua
sendServerCommand("MyMod", "evt", { x = 1 })            -- broadcast
sendServerCommand(player, "MyMod", "evt", { x = 1 })    -- one player

Events.OnServerCommand.Add(function(module, command, args)
    if module ~= "MyMod" then return end
end)
```

Argument signatures verified in the decompiled Java: `OnClientCommand(module, command, player, args)`, `OnServerCommand(module, command, args)`. In single-player the game routes these through `SinglePlayerServer`/`SinglePlayerClient`, so the same code works offline.

Vanilla examples: `server/ClientCommands.lua`, `server/Camping/camping_tent.lua`, and `shared/Util/LuaNet.lua` (a higher-level wrapper).

## Gotchas

- `OnPlayerUpdate` fires for the local player on a client but for every player on the server.
- Args tables must hold simple values (numbers, strings, booleans, nested tables), not Java objects; send IDs or coordinates and resolve them server-side.
- Test item duplication on abrupt disconnect for mods that force-equip or move items.
- For custom actions use timed actions (`pz-timed-actions-ui`), which vanilla already makes network-aware.
