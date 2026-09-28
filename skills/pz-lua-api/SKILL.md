---
name: pz-lua-api
description: Project Zomboid Lua-callable Java API for players, inventory items, containers, squares and mod data, with pointers to the decompiled classes. Use when writing Lua that calls game objects.
---

# Lua-callable Java API

Public methods of the game's Java classes are callable from Lua with `obj:method(...)`. Lua tables are 1-indexed but Java `ArrayList`s are 0-indexed:

```lua
local items = container:getItems()
for i = 0, items:size() - 1 do
    local it = items:get(i)
end
```

## Classes (in `zombie/` of the decompiled output)

| Lua object | Java file |
|---|---|
| player / character | `characters/IsoPlayer.java`, `characters/IsoGameCharacter.java` |
| zombie | `characters/IsoZombie.java` |
| item | `inventory/InventoryItem.java`; subtypes in `inventory/types/` (`Food`, `Clothing`, `HandWeapon`, `InventoryContainer`, `DrainableComboItem`, ...) |
| container | `inventory/ItemContainer.java` |
| grid square | `iso/IsoGridSquare.java` |
| world object | `iso/IsoObject.java` and `iso/objects/*` |
| global functions | `Lua/LuaManager.java` |
| events | `Lua/LuaEventManager.java` |
| game time | `GameTime.java` |
| sandbox options | `SandboxOptions.java` (Lua: `SandboxVars.MyMod.Option`) |

## Common calls (confirmed in the guide and vanilla)

```lua
local player = getPlayer()                  -- local player 0
local p = getSpecificPlayer(0)
player:getPrimaryHandItem() / getSecondaryHandItem()
player:setPrimaryHandItem(nil)              -- unequip (used in ISUnequipAction)
player:getInventory()                       -- ItemContainer
player:isAiming(); player:getVehicle()
player:getX(), player:getY(), player:getZ()
item:getFullType()   -- "Base.Plank";  item:getType() -- "Plank"
item:getContainer(); item:getCondition(); item:getDisplayName()
item:isTwoHandWeapon() or item:isRequiresEquippedBothHands()
container:AddItem("Base.Plank"); container:Remove(item); container:contains(item)
container:getCapacity(); container:getItems()
InventoryItemFactory.CreateItem("Base.Plank")
ModData.getOrCreate("MyMod")
```

## Verification rule

Method names change between builds. Before using a method not listed above, grep the Java class for its declaration (`grep -n "public .* methodName(" <file>`). If it is absent, look for the vanilla Lua usage instead of guessing. Use `pz-source-lookup` for paths.

## Style

- Check `nil` from getters (`getPrimaryHandItem()` is often nil).
- Compare item identity with `getFullType()` strings, define them as constants.
- Never mutate authoritative state on a client in multiplayer: see `pz-multiplayer`.
