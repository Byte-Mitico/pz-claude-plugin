---
name: pz-mod-structure
description: Project Zomboid Build 42 mod folder layout, mod.info, workshop.txt, versioned 42/ folder, modules and translations. Use when creating a mod, fixing a mod that will not load, or converting a Build 41 mod.
---

# Mod structure (Build 42)

```
MyMod/                         <- Workshop root
├── workshop.txt
├── preview.png
└── Contents/mods/MyMod/
    ├── common/                (optional, loaded by all builds)
    └── 42/
        ├── mod.info
        ├── poster.png
        └── media/
            ├── scripts/       item and craftRecipe .txt
            ├── lua/{client,server,shared}/
            ├── textures/      item_<Icon>.png icons
            └── models/
```

Local testing: place the mod at `~/Zomboid/mods/<id>/` with the same inner layout (`42/mod.info`, `42/media/...`). B42 only loads `42/`; a root-level `media/` is ignored (the #1 cause of "mod does not load").

## mod.info

```
name=My Mod
id=MyModID
author=Name
description=Shown in the mod list.
poster=poster.png
require=OtherModID
```

`id` has no spaces. `mod.info` lives in `42/`, not the root. `require` is optional.

## workshop.txt

```
version=1
workshopid=0
title=My Mod
description=...
visibility=public
tags=Build 42;Items
```

`workshopid=0` until first publish.

## Modules

Scripts declare `module <Name> { ... }`; items are referenced as `Module.ItemID`. Use a custom module for anything published (avoids ID collisions) and add `imports { Base }` to reference vanilla items.

## Translations

Vanilla B42 translations are JSON in `media/lua/shared/Translate/<LANG>/` (`ItemName.json` with `"Base.Plank": "Plank"`, `ContextMenu.json`, `Sandbox.json`, `Tooltip.json`, `IG_UI.json`). Mirror that in the mod, e.g. `42/media/lua/shared/Translate/EN/ItemName.json`. Check the vanilla file for the key convention before adding keys.

## B41 to B42 migration checklist

1. Move `media/` under `42/`; add `42/mod.info`.
2. `Type = X` becomes `ItemType = base:x` (see `pz-item-scripts`).
3. `recipe {}` becomes `craftRecipe {}` (see `pz-craft-recipes`).
4. Recheck item IDs and tags (e.g. `TreeBranch` is now `TreeBranch2`).
5. Run the game and read the console/log for script parse errors.

Use `/pz-new-mod` to scaffold this layout.

## Testing

- Enable the mod in the Mods menu; use a Sandbox save with high XP for recipe testing.
- Launch with `-debug` for the debug tools. Logs are in `~/Zomboid/Logs/`.
- Lua can be reloaded without restarting; item and recipe scripts need a full restart.
