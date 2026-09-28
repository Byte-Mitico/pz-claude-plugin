---
name: pz-source-lookup
description: Locate ground truth for Project Zomboid modding questions in the vanilla Lua, the decompiled Java and the vanilla scripts. Use before answering any API, event, script-property or item-ID question, or when a Lua/Java method's existence or signature is uncertain.
---

# Looking things up in the game source

Never guess a PZ API, event signature, item ID or script property. Verify it against the sources below. Build 41 tutorials are often wrong for Build 42.

## Source locations (defaults on the plugin author's machine)

| What | Path |
|---|---|
| Vanilla Lua | `~/.local/share/Steam/steamapps/common/ProjectZomboid/projectzomboid/media/lua/{client,server,shared}` |
| Vanilla scripts (items, recipes, sounds, models) | `.../projectzomboid/media/scripts` (items in `scripts/generated/items/*.txt`, recipes under `scripts/generated/entities/**`) |
| Vanilla translations (JSON) | `.../media/lua/shared/Translate/<LANG>/*.json` |
| Decompiled Java | `~/Projects/ProjectZomboid/ZomboidDecompiler/bin/output/zombie` |
| Community guide | https://github.com/gotmayonase/pz-modding-guide |

If a path does not exist, ask the user where it is instead of continuing blind. The decompiled Java is read-only reference; do not edit it.

## Where to look

- **Event exists / arguments?** `zombie/Lua/LuaEventManager.java` (`AddEvent("...")` list), then grep `triggerEvent("EventName"` across `zombie/` to see the exact arguments passed.
- **Java method callable from Lua?** Read the class: `zombie/characters/IsoPlayer.java`, `IsoGameCharacter.java`, `zombie/inventory/InventoryItem.java`, `ItemContainer.java`, `zombie/inventory/types/*`, `zombie/iso/IsoGridSquare.java`, `IsoObject`. Global functions (`getPlayer`, `sendClientCommand`, ...) live in `zombie/Lua/LuaManager.java`.
- **How does vanilla do X?** Grep the vanilla Lua for a similar feature. Good starting points: `client/TimedActions/*`, `client/ISUI/*`, `server/ClientCommands.lua`, `shared/Util/LuaNet.lua`.
- **Item property valid?** Find a vanilla item using it in `scripts/generated/items/`, and check the parser under `zombie/scripting/`.
- **Item ID / tag / skill ID?** Grep `scripts/`; do not rely on memory (B42 renamed many IDs, e.g. `Base.TreeBranch2`).

## Search habits

- `grep -rn "methodName(" <java dir> --include=*.java` for methods; `grep -rn "Events.OnX" <lua dir>` for usage examples.
- Cite the file (and line) of the evidence when stating an API fact.
