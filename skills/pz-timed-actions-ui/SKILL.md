---
name: pz-timed-actions-ui
description: Project Zomboid timed actions (ISBaseTimedAction, ISTimedActionQueue) and context menus / ISUI panels. Use when adding a player action with a progress bar, a right-click menu entry, or a UI window.
---

# Timed actions

Base class: `media/lua/shared/TimedActions/ISBaseTimedAction.lua`. Vanilla examples: `client/TimedActions/*.lua` (e.g. `ISOpenContainerTimedAction.lua`).

```lua
require "TimedActions/ISBaseTimedAction"

MyAction = ISBaseTimedAction:derive("MyAction")

function MyAction:isValid() return true end   -- checked every tick; false cancels
function MyAction:start() end                 -- set animation here (self:setActionAnim(...))
function MyAction:update() end
function MyAction:stop()  ISBaseTimedAction.stop(self)  end   -- interrupted
function MyAction:perform()
    -- do the work, then:
    ISBaseTimedAction.perform(self)           -- required, advances the queue
end

function MyAction:new(character, item, time)
    local o = ISBaseTimedAction.new(self, character)
    o.item = item
    o.maxTime = time
    return o
end
```

Queue it: `ISTimedActionQueue.add(MyAction:new(player, item, 50))`. To walk first, add a `luautils.walkAdj(player, square)` before it (see vanilla). `isValidStart`, `waitToStart`, `forceComplete`, `getJobDelta` are also available on the base class.

Because state changes must happen on the server in MP, see `pz-multiplayer`; look at how vanilla actions with server counterparts are structured before adding one.

# Context menus

```lua
local function onFillInv(playerNum, context, items)
    -- items may hold InventoryItems or wrapper tables; vanilla normalizes them
    -- (see client/ISUI/ISInventoryPaneContextMenu.lua)
    context:addOption(getText("ContextMenu_MyOption"), items, onSelect, playerNum)
end
Events.OnFillInventoryObjectContextMenu.Add(onFillInv)

local function onFillWorld(playerNum, context, worldObjects, test)
    if test then return true end   -- vanilla convention for joypad test pass
    context:addOption("My action", worldObjects, fn)
end
Events.OnFillWorldObjectContextMenu.Add(onFillWorld)
```

`ISContextMenu:addOption(name, target, onSelect, p1..p10)` and `addSubMenu(option, menu)` / `getNew(parentContext)` are defined in `client/ISUI/ISContextMenu.lua`. Use `getText("Key")` with keys from your `ContextMenu.json` translation.

# UI panels

Windows and widgets are in `client/ISUI/` (`ISPanel`, `ISButton`, `ISCollapsableWindow`, `ISComboBox`, `ISModalDialog`...). Pattern: derive with `ISPanel:derive("MyPanel")`, implement `initialise`, `createChildren`, `render`, then `panel:initialise(); panel:addToUIManager()`. Read a small vanilla panel (e.g. `ISAlarmClockDialog.lua`) and copy its structure.
