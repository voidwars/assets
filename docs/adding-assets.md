# Adding assets

## New custom item icon

Typical flow for a new game item that uses an existing CMD range and host material.

### 1. Allocate the CMD ID (voidwars-platform)

In `VoidWarsModelData.java`, add an enum entry in the correct region with the next free ID. Example:

```java
MY_NEW_HERB(857),
```

Use the server PR to land the ID before or alongside the pack change.

### 2. Create the texture

- Path: `pack/assets/minecraft/textures/item/<name>.png`
- Match the resolution and style of similar icons (most item textures are small pixel art or Blockbench UV layouts).
- Filename must match the model basename.

### 3. Create the model

- Path: `pack/assets/minecraft/models/item/<name>.json`
- Export from Blockbench using the existing item preset, or copy a similar model and adjust.
- Flat icons use `"parent": "item/generated"` with `"layer0": "item/<name>"`.
- 3D weapons/tools use element geometry — see `flint_sword.json` for a reference.
- Set `"display"."gui"` rotation/translation so the icon is centred in inventory.

### 4. Wire CMD dispatch

Open the host item file for your CMD range (see [custom-model-data.md](custom-model-data.md)) and add a sorted entry:

```json
{
    "threshold": 857,
    "model": {
        "type": "model",
        "model": "item/my_new_herb"
    }
}
```

File: e.g. `pack/assets/minecraft/items/raw_iron.json` for gatherable nodes (801+).

### 5. Test

1. Load `pack/` as a resource pack in Minecraft 1.21.4.
2. On a dev server, give yourself the host item with the CMD:  
   `/give @s raw_iron[custom_model_data=857]`
3. Confirm inventory, ground, and hand displays.

## New GUI icon

GUI icons (stash controls, pagination, etc.) follow the same steps but usually:

- Host: `paper.json` (1400 band) or another UI host as defined in `VoidWarsModelData`.
- Scope commits as `feat(gui): …` — see [contributing.md](contributing.md).
- Many GUI models are simple Blockbench cubes mapped to a single texture (`gui_trash.json`).

## New skill icon

- Model: `models/item/skill_<name>.json`
- Texture: `textures/item/skill_<name>.png`
- CMD: 1201–1255 range on **`brick.json`** host
- Enum: `SKILL_<NAME>` in `VoidWarsModelData`

## New ModelEngine mob

1. Export bones to `pack/assets/modelengine/models/<mob_id>/`.
2. Place entity texture at `pack/assets/modelengine/textures/entity/<mob_id>.png`.
3. Configure the mob in ModelEngine on the server (outside this repo).
4. If the mob needs an inventory preview, add entries on `leather_horse_armor.json` using `"model": "modelengine:<mob_id>/<bone>"`.

## New cartridge icon

- Models: `models/item/cartridge_<n>.json` (flat generated parent)
- Textures: `textures/item/cartridge_<n>.png`
- CMD: 1700–1713 on **`brick.json`** (shared with secure-container enum names in Java)

## Checklist before opening a PR

- [ ] JSON validates (no trailing commas)
- [ ] Texture path in model matches an existing PNG
- [ ] CMD threshold matches `VoidWarsModelData.dataId`
- [ ] Entry inserted in ascending threshold order
- [ ] Tested in client 1.21.4 with pack loaded
- [ ] Commit message follows [contributing.md](contributing.md)
