# Custom model data

The server assigns `custom_model_data` (CMD) on vanilla host items. The pack maps each CMD value to a Blockbench model via `range_dispatch` in `pack/assets/minecraft/items/<host>.json`.

**Source of truth for numeric IDs:** `VoidWarsModelData` in voidwars-platform:

`services/paper-server/src/main/java/org/sirprimrose/voidwars/game/items/VoidWarsModelData.java`

When adding a new icon, allocate the ID in that enum first, then wire the same threshold in the correct host item JSON.

## CMD ranges

| Range | Category | Examples |
|-------|----------|----------|
| 1–12 | Bow / crossbow classes | `BOW_CLASS_1` … `CROSSBOW_CLASS_6` |
| 101–166 | Melee weapons and wands | swords, daggers, maces, spears, hammers, wands |
| 201–266 | Tools | pickaxes, quarry hammers, sickles, lumber axes, secateurs, harpoons, skinning knives |
| 301–306 | Currency | copper through netherite coins, contract points |
| 401–421 | Health items | bandages, medkits, splints |
| 501–510 | Mob pool tickets | `mobpool_*` icons |
| 601–625 | Container loot icons | `container_*` (paper host) |
| 701–724 | Loose loot icons | `loose_*` (item_frame host) |
| 801–866 | Gatherable world nodes | ore clusters, patches, stumps, herb nodes, fish schools |
| 901–966 | Raw gatherable drops | ores, fibers, logs, hides, fish |
| 1001–1066 | Processed gatherables | ingots, cloth, leather, bricks, blends, scales |
| 1101–1107 | Essences | fire, ice, air, earth, lightning, light, dark |
| 1201–1255 | Skill icons | `skill_*` |
| 1301 | Streamer / misc | `MF_HELMET` |
| 1401–1406 | UI elements | exploded mine, searching icon, pagination, mailbox, trash |
| 1501–1518 | Armor tier icons | light / medium / heavy × 6 tiers |
| 1601–1612 | Contract task heads | blaze, bosses, mob heads |
| 1700–1713 | Cartridges / secure containers | `cartridge_0` … `cartridge_13` (brick host) |

Gaps in numbering (e.g. 622, 722) are intentional spares or reserved slots — follow existing patterns in `VoidWarsModelData` rather than inventing new numbers.

## Host items

Each host item JSON centralises CMD dispatch for one vanilla material:

| Host (`items/<file>.json`) | CMD ranges handled |
|----------------------------|-------------------|
| `stick.json` | 101–166 (weapons/tools on stick) |
| `bow.json` | 1–6 (with pull-state composites) |
| `crossbow.json` | 7–12 (with charge/firework composites) |
| `trident.json` | 113–118 (tiered spears — legacy tier display) |
| `gold_nugget.json` | 301–306 |
| `golden_apple.json` | 401–402, 411, 421 |
| `name_tag.json` | 501–510 |
| `paper.json` | 601–625, 1403–1406 |
| `item_frame.json` | 701–724 |
| `raw_iron.json` | 801–866 |
| `brick.json` | 901–966, 1001–1066, 1101–1107, 1201–1255, 1401, 1700–1713 |
| `chainmail_helmet.json` | 1501–1518 |
| `chainmail_chestplate.json` | 1501–1518 |
| `chainmail_leggings.json` | 1501–1518 |
| `chainmail_boots.json` | 1501–1518 |
| `netherite_pickaxe.json` | 304, 1301, 1402, 1601–1612 |
| `player_head.json` | 1–8 (custom player body part models) |
| `leather_horse_armor.json` | low IDs for ModelEngine mob previews |

All armor slot host files share the same 1501–1518 mapping — the server picks the host material matching the armor piece being shown.

## Range dispatch rules

- **`threshold`** is the minimum CMD value for that entry; entries must be sorted ascending.
- **`fallback`** is the model when CMD is below the first threshold (usually the vanilla item model).
- **`oversized_in_gui": true`** is set on most custom icons so Blockbench models display correctly in inventory slots.
- Bow/crossbow hosts use nested composites (`minecraft:condition`, pull/charge properties) — copy an existing tier entry when adding a new class.

## Keeping server and pack in sync

1. Add enum constant + ID in `VoidWarsModelData`.
2. Add `"threshold"` entry pointing at `item/<model_name>` in the correct host JSON.
3. Add `models/item/<model_name>.json` and `textures/item/<model_name>.png`.
4. Verify in-game on a dev server (Minecraft 1.21.11) with this pack loaded.

If the CMD exists in the enum but not in the pack (or vice versa), the client shows the wrong or vanilla model.
