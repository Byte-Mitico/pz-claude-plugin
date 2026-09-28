---
name: pz-controller-input
description: Project Zomboid gamepad/joypad support in Lua. Use when polling controller buttons or sticks, reacting to controller connect/activate events, or making a mod's UI panel navigable and closable with a controller (joypad focus, ISPanelJoypad, ISUIElementJoypad).
---

# Controller (joypad) input

Sources: `client/ISUI/Gamepad/JoyPadSetup.lua` (constants, focus, `JoypadState`), `client/ISUI/ISPanelJoypad.lua`, `client/ISUI/ISUIElementJoypad.lua`; Java globals in `zombie/Lua/LuaManager.java`, events in `zombie/Lua/LuaEventManager.java`. Client side only.

There is no per-button event. Button presses are delivered as `onJoypadDown(button, joypadData)` to the UI element that has joypad focus. Use polling globals only for held input (sticks, triggers).

## Is this player on a controller?

```lua
local jd = getJoypadData(playerNum)   -- JoypadState.players[playerNum+1], nil = keyboard/mouse
```

`playerNum` is the local split-screen index (0-3). It is not the joypad id; go through `getJoypadData`, and use `jd.id`-style fields only after reading `JoypadData` in `JoyPadSetup.lua`.

## Events

Declared in `LuaEventManager.java`; arguments from the `triggerEvent` calls in `input/JoypadManager.java`, `core/input/Input.java`, `GameWindow.java`.

| Event | Args |
|---|---|
| `OnGamepadConnect`, `OnGamepadDisconnect` | controller id |
| `OnJoypadActivate`, `OnJoypadActivateUI` | joypad id |
| `OnJoypadBeforeDeactivate`, `OnJoypadDeactivate` | joypad id |
| `OnJoypadBeforeReactivate`, `OnJoypadReactivate` | joypad id |
| `OnJoypadRenderUI` | none |

## Button constants

`Joypad.AButton BButton XButton YButton LBumper RBumper Back Start LStickButton RStickButton Other DPadLeft DPadRight DPadUp DPadDown` (`JoyPadSetup.lua:12-28`). Compare against these in `onJoypadDown`; do not hard-code numbers, since the mapping is per controller.

## Polling globals (`LuaManager.java`)

- Buttons: `isJoypadPressed(id, button)`, `isJoypadLTPressed(id)`, `isJoypadRTPressed(id)`, `isJoypadLBPressed`, `isJoypadRBPressed`, `isJoypadLeftStickButtonPressed`, `isJoypadRightStickButtonPressed`.
- D-pad: `isJoypadUp/Down/Left/Right(id)`.
- Sticks: `getJoypadMovementAxisX/Y(id)`, `getJoypadAimingAxisX/Y(id)`.
- Device: `isJoypadConnected(id)`, `getControllerName(id)`, `getControllerGUID(id)`, `isXBOXController(id)`, `isPlaystationController(id)`, `getControllerCount()`.
- Raw: `getControllerAxisValue`, `getControllerPovX/Y`, `getControllerDeadZone`/`setControllerDeadZone`.

Verify a signature in `LuaManager.java` (grep `name = "isJoypad`) before relying on it.

## Focus system

- `setJoypadFocus(playerNum, element)` gives an element focus; `nil` clears it. It remembers the previous focus.
- `getJoypadFocus(playerNum)` returns the current one; `setPrevFocusForPlayer(playerNum)` restores the previous.
- The focused element receives `onGainJoypadFocus(joypadData)` / `onLoseJoypadFocus(joypadData)` (base `ISUIElement` sets `self.joyfocus`), then `onJoypadDown(button, joypadData)` and `onJoypadDirUp/Down/Left/Right(joypadData)`.

## Making a panel controller-friendly

Derive from `ISPanelJoypad` (needs `require "ISUI/ISPanelJoypad"`), lay buttons out in rows with `insertNewLineOfButtons(...)` (D-pad moves between them, A clicks the focused button), and optionally bind face buttons with `setISButtonForA/B/X/Y(button)`, which also draws the prompt icon. Vanilla references: `ISAlarmClockDialog.lua`, `ISSleepDialog.lua`.

```lua
require "ISUI/ISPanelJoypad"
MyPanel = ISPanelJoypad:derive("MyPanel")

function MyPanel:createChildren()
    -- ... create self.ok / self.cancel with ISButton ...
    self:insertNewLineOfButtons(self.ok, self.cancel)
    self:setISButtonForB(self.cancel)      -- B triggers cancel
end

function MyPanel:onGainJoypadFocus(joypadData)
    ISPanelJoypad.onGainJoypadFocus(self, joypadData)
    self.joypadIndexY, self.joypadIndex = 1, 1
    self.joypadButtons = self.joypadButtonsY[1]
    self.joypadButtons[1]:setJoypadFocused(true)
end

function MyPanel:onJoypadDown(button, joypadData)
    ISPanelJoypad.onJoypadDown(self, button, joypadData)   -- handles A/B/X/Y bindings
end
```

Open and close, the way vanilla does (`ISInventoryPaneContextMenu.lua:2625`, `ISAlarmClockDialog.lua:100`):

```lua
local panel = MyPanel:new(0, 0, 230, 160, playerNum)
panel:initialise(); panel:addToUIManager()
if getJoypadData(playerNum) then
    panel.prevFocus = getPlayerInventory(playerNum)   -- or getJoypadFocus(playerNum)
    setJoypadFocus(playerNum, panel)
end

-- on close:
panel:removeFromUIManager()
if getJoypadData(playerNum) then setJoypadFocus(playerNum, panel.prevFocus) end
```

Always restore focus on close; otherwise the controller player is left with input routed to a dead element.

## ISUIElementJoypad (alternative)

`ISUIElementJoypad` is a mixin/wrapper (header comment in `ISUIElementJoypad.lua`) for hierarchies where every element takes part in focus traversal: `ISUIElementJoypad.Wrap(ISButton, ...)`, `setBucket`, `setZOrder`, `orderJoypadChildren`. Instead of one `onJoypadDown` chain, register callbacks with `setEventCallback("onAButton", func, target)` (events: `onAButton onBButton onXButton onYButton onLBumper onRBumper onJoypadDirUp/Down/Left/Right`, dispatched at lines ~390-406); `setEventPromptText` sets prompt text. Prefer `ISPanelJoypad` for a simple dialog, since it is the more common vanilla pattern.

## Pitfalls

- Context-menu fill events run a test pass for joypads: return `true` early when `test` is set (see `pz-timed-actions-ui`).
- Controller players and keyboard players can coexist in split-screen; check `getJoypadData(playerNum)` per player instead of a global flag.
- Cite the vanilla file when stating an API fact; see `pz-source-lookup`.
