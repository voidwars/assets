# Adding assets

## New custom item icon

### 1. Allocate CMD ID (voidwars-platform)

Add an enum entry in `VoidWarsModelData.java` in the correct range:

```java
MY_NEW_HERB(857),
```

Land the platform change before or alongside the pack PR.

### 2. Texture

`pack/assets/minecraft/textures/item/<name>.png` — match style of similar icons; basename must match the model.

### 3. Model

`pack/assets/minecraft/models/item/<name>.json`

- Flat icons: `"parent": "item/generated"` + `"layer0": "item/<name>"`
- 3D items: Blockbench element model — see `flint_sword.json`
- Adjust `"display"."gui"` so the icon sits correctly in inventory

### 4. Wire CMD dispatch

Add a sorted `"threshold"` entry in the correct host file (`pack/assets/minecraft/items/<host>.json`):

```json
{
    "threshold": 857,
    "model": {
        "type": "model",
        "model": "item/my_new_herb"
    }
}
```

Host ↔ range mapping: [custom-model-data.md](custom-model-data.md).

### 5. Test

1. Load `pack/` in Minecraft **1.21.11**.
2. `/give @s raw_iron[custom_model_data=857]` (swap host item as needed).
3. Check inventory, ground, and hand display.

## Other asset types

| Type | Model | Texture | Host / CMD |
|------|-------|---------|------------|
| GUI icon | `models/item/gui_<name>.json` | `textures/item/gui_<name>.png` | `paper.json` (1400 band) |
| Skill icon | `models/item/skill_<name>.json` | `textures/item/skill_<name>.png` | `brick.json` (1201–1255) |
| Cartridge | `models/item/cartridge_<n>.json` | `textures/item/cartridge_<n>.png` | `brick.json` (1700–1713) |
| Armor **icon** | `models/item/<tier>_<set>_*.json` | `textures/item/…` | `chainmail_*.json` (1501–1518) |
| Armor **worn** + trims | (trim atlas, not CMD) | `textures/entity/equipment/…` + `textures/trims/…` | see [armor-and-trims.md](armor-and-trims.md) |
| ModelEngine mob | `modelengine/models/<id>/` | `modelengine/textures/entity/<id>.png` | server-side ModelEngine config |

## PR checklist

- [ ] Changes under `pack/` only
- [ ] JSON valid (no trailing commas, paths resolve)
- [ ] CMD threshold matches `VoidWarsModelData`
- [ ] Threshold entries sorted ascending in host JSON
- [ ] Tested locally in Minecraft 1.21.11
- [ ] Platform PR linked if CMD IDs were added
- [ ] QA on staging server after PR publishes `staging`
- [ ] Ready for production on merge (`latest` updates automatically)
