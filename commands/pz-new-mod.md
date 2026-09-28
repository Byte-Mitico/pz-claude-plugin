---
description: Scaffold a Project Zomboid Build 42 mod (Workshop layout, mod.info, sample item and recipe)
argument-hint: <ModId> [target-directory]
---

Scaffold a new Project Zomboid Build 42 mod named `$1`. Target directory: `$2` if given, otherwise `./$1`.

Follow the `pz-mod-structure`, `pz-item-scripts` and `pz-craft-recipes` skills. Create:

- `workshop.txt` (`workshopid=0`) at the root
- `Contents/mods/$1/42/mod.info` with `id=$1` (no spaces) and a placeholder name/author
- `Contents/mods/$1/42/media/scripts/items.txt` with module `$1`, `imports { Base }` and one example `ItemType = base:normal` item
- `Contents/mods/$1/42/media/scripts/recipes.txt` with one `craftRecipe` producing that item from a verified vanilla item, using `mode:keep` on any tool
- empty `media/lua/{client,server,shared}/` and `media/textures/` directories (with `.gitkeep`)
- `Contents/mods/$1/42/media/lua/shared/Translate/EN/ItemName.json` naming the example item

Verify every vanilla item ID and tag you reference by grepping the vanilla scripts (see `pz-source-lookup`). Do not overwrite an existing directory without asking. Finish by listing the created tree and telling the user to symlink or copy `Contents/mods/$1` into `~/Zomboid/mods/` to test.
