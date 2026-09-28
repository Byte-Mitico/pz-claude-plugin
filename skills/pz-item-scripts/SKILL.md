---
name: pz-item-scripts
description: Write Project Zomboid Build 42 item scripts (item blocks, ItemType, container/clothing/weapon properties, icons, tags). Use when adding or editing items in media/scripts/*.txt.
---

# Item scripts (B42)

Files: `42/media/scripts/*.txt`, any number, all loaded at startup (restart needed after changes).

```
module MyMod
{
    imports { Base }

    item MyItem
    {
        DisplayName     = My Item,
        DisplayCategory = Material,
        ItemType        = base:normal,
        Weight          = 1.0,
        Icon            = MyIcon,            // media/textures/item_MyIcon.png
        Tags            = base:holdgeneral,
    }
}
```

Properties are `Name = value,` (comma-terminated). Comments use `//`.

## ItemType values

`base:normal`, `base:container`, `base:food`, `base:weapon`, `base:clothing`, `base:drainable` (uses-based, e.g. Twine), and others. Grep `scripts/generated/items/*.txt` for `ItemType =` to see all in use. The B41 `Type =` syntax is invalid.

## Container example (mirrors vanilla `container.txt`)

```
item Bag_Example
{
    DisplayCategory  = Bag,
    ItemType         = base:container,
    Weight           = 1.2,
    Capacity         = 18,
    WeightReduction  = 65,          // contents weigh 35%
    RunSpeedModifier = 0.95,
    CanBeEquipped    = base:back,   // worn bags
    OpenSound = OpenBag, CloseSound = CloseBag, PutInSound = PutItemInBag,
    ReplaceInPrimaryHand = ModelName holdingbagright,
    ReplaceInSecondHand  = ModelName holdingbagleft,
    WorldStaticModel = ModelName,
}
```

- `RequiresEquippedBothHands = true` occupies both hands for any item type. `TwoHandWeapon = TRUE` works for weapons only; on a container it does not behave as expected.
- `RunSpeedModifier` from all worn/held items stacks additively (differences from 1.0 are summed).
- The community guide reports vanilla caps `Capacity` at 100.

## Rules

- Never invent property names: find a vanilla item using the property first (`grep -rn "PropName" <media>/scripts`).
- Display names come from translation `ItemName.json` keyed `Module.ItemID`; `DisplayName` is a fallback.
- `Icon = Foo` maps to `media/textures/item_Foo.png`.
- Check referenced vanilla IDs in `scripts/` (e.g. `Base.Pipe`, not `Base.MetalPipe`).
