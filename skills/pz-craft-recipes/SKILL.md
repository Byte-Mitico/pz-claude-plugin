---
name: pz-craft-recipes
description: Write Project Zomboid Build 42 craftRecipe scripts (inputs, outputs, tags, skills, OnCreate/OnTest). Use when adding crafting recipes; the B41 `recipe {}` syntax no longer works.
---

# craftRecipe (B42)

```
module MyMod
{
    imports { Base }

    craftRecipe MakeMyItem
    {
        timedAction   = Making,
        Time          = 300,                 // tenths of a second: 30s
        category      = Carpentry,
        SkillRequired = Woodwork:3;Blacksmith:1,
        xpAward       = Woodwork:60,
        Tags          = InHandCraft,
        needToBeLearn = false,
        // OnCreate = Recipe.OnCreate.MyFunc,   // Lua after craft
        // OnTest   = Recipe.OnTest.MyFunc,     // Lua availability check

        inputs
        {
            item 4 [Base.Plank],
            item 1 tags[base:hammer] mode:keep,
            item 1 tags[base:saw] mode:keep flags[MayDegradeLight],
        }

        outputs
        {
            item 1 MyMod.MyItem,
        }
    }
}
```

## Rules

- Inputs are **consumed by default**; add `mode:keep` for tools.
- Input IDs use brackets `[Module.Item]`; outputs do not.
- Prefer `tags[...]` for tools so other mods' tools work. Tags seen in vanilla recipes: `base:hammer`, `base:saw`, `base:sharpknife`, `base:sewingneedle`, `base:binding`, `base:twine`, `base:fishingline`. Confirm others by grepping `scripts/`.
- Skill IDs differ from display names: Carpentry = `Woodwork`, Metalworking = `Blacksmith`, First Aid = `Doctor`; `Masonry`, `Carving`, `Tailoring`, `Farming`, `Aiming` match. Separate multiple skills with `;`.
- `base:drainable` inputs (Twine) count items, not uses.
- An upgrade path is a recipe that consumes the previous tier as a normal input.
- Mirror vanilla examples under `scripts/generated/entities/**/*.txt` (e.g. `craftrecipe_kiln.txt`) and verify unfamiliar fields against the parser under `zombie/scripting/` in the decompiled Java.
