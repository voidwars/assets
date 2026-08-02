# Pack structure

## Top level

```
assets/
├── pack/                    ← ship this (CI input)
│   ├── pack.mcmeta
│   ├── pack.png
│   └── assets/
│       ├── .mcassetsroot    ← MC Assets editor root (optional)
│       ├── minecraft/       ← minecraft: namespace
│       └── modelengine/     ← modelengine: namespace
└── .github/workflows/       ← PackSquash CI
```

**Always edit `pack/`.** CI runs PackSquash on `pack/` only — nothing outside that folder is released.

For MC Assets / Blockbench workflows, open `pack/assets/` (marked by `.mcassetsroot`).

## `pack/assets/minecraft/`

| Directory | Contents |
|-----------|----------|
| `items/` | 1.21+ item model definitions with `custom_model_data` range dispatch |
| `models/item/` | Per-icon Blockbench models (`item/<name>`) |
| `models/custom/` | Special models (e.g. dismembered player body parts) |
| `textures/item/` | Item and GUI icon PNGs |
| `textures/gui/` | HUD and GUI sprites |
| `textures/entity/` | Entity texture overrides |
| `textures/trims/` | Custom armor trim palettes and models |
| `textures/misc/` | Misc texture overrides |
| `textures/models/armor/` | **Legacy** armor layers (pre-1.21.2 paths; keep in sync with equipment/) |
| `textures/entity/equipment/humanoid/` | **Worn** body armor textures (1.21.2+) |
| `textures/entity/equipment/humanoid_leggings/` | **Worn** leg armor textures (1.21.2+) |
| `atlases/` | Sprite atlas configuration (`armor_trims.json`, `blocks.json`) |
| `lang/en_us.json` | Display names for custom trim materials and patterns |
| `shaders/core/` | Core shader overrides (entity translucency / player parts) |

Worn armor + trims: [armor-and-trims.md](armor-and-trims.md).

## `pack/assets/modelengine/`

ModelEngine exports for custom mobs. Each mob has bone JSON under `models/<mob_id>/` and textures under `textures/entity/`.

Current mobs:

| ID | Bones |
|----|-------|
| `vw_stone_gargoyle` | body, head, limbs, wings, face |
| `vw_stone_sister` | upperbody, arms, eyes |
| `vw_mimic` | tongue1–3 |

Mob preview items in GUIs reference these via `modelengine:` model paths on the `leather_horse_armor` host item.

## Asset naming

- **Items / icons**: `snake_case`, grouped by prefix — `skill_*`, `container_*`, `loose_*`, `mobpool_*`, `gui_*`, `<material>_<tool>`, etc.
- **Textures**: same basename as the model they bind to, under `textures/item/<name>.png`.
- **Models**: `models/item/<name>.json` referenced as `"model": "item/<name>"` in item definitions.

## Item model pipeline

Custom icons use the 1.21+ item definition format (`pack/assets/minecraft/items/`):

1. A **host** vanilla item (`stick`, `paper`, `brick`, …) has an entry in `items/<host>.json`.
2. That file uses `range_dispatch` on `custom_model_data` to pick a Blockbench model.
3. The server sets CMD on the host material via `VoidWarsModelData`.

See [custom-model-data.md](custom-model-data.md) for the host ↔ CMD mapping and [adding-assets.md](adding-assets.md) for the step-by-step workflow.
