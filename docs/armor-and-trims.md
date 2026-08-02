# Armor and trims (worn look)

## What you are looking at on an armor stand

Voidwars loot armor is **`CHAINMAIL_*` items with an armor trim component**, not a separate custom mesh when worn.

| Layer | Source | What it looks like |
|-------|--------|--------------------|
| **Inventory icon** | CMD → Blockbench model (`items/chainmail_*.json`) | Custom 2D/3D icon (this was working) |
| **Worn base** | Equipment texture `chainmail` | Thin vanilla chain silhouette |
| **Worn “armor style”** | **Trim pattern × material** (padded / chainmail / platemail + coarse…netherite) | Almost all of the visible worn look |

So purple/black **on armor stands / players / mobs** with good icons is almost always a **worn-layer / trim-atlas** problem, not CMD.

## 1.21.2+ path rules (pack format 75 / MC 1.21.11)

### Equipment (base chainmail / leather)

| Role | Path |
|------|------|
| Body | `textures/entity/equipment/humanoid/<material>.png` |
| Legs | `textures/entity/equipment/humanoid_leggings/<material>.png` |
| Leather overlay | `…/leather_overlay.png` in each of the above dirs |

Legacy `textures/models/armor/*_layer_*` is ignored for worn rendering.

### Trim patterns (this is the stand-critical path)

Vanilla moved trims off `trims/models/armor/`:

| Role | Path |
|------|------|
| Body pattern | `textures/trims/entity/humanoid/<pattern>.png` |
| Legs pattern | `textures/trims/entity/humanoid_leggings/<pattern>.png` |

**Note:** leggings file is named `<pattern>.png` inside the leggings folder (not `<pattern>_leggings.png`).

`atlases/armor_trims.json` must list those same paths (plus palettes). Providing this file **replaces** the vanilla atlas, so it must include vanilla patterns too or those also break.

Custom Voidwars patterns: `padded`, `chainmail`, `platemail`.  
Custom materials (palettes): `coarse`, `rugged`, `steeled`, `mighty`, `robust`, `reinforced`, `flint`.

### Server mapping (platform)

`ArmorConsts` + `ArmorStats.trimArmor`:

| Armor type | Pattern id | Materials by tier |
|------------|------------|-------------------|
| LIGHT | `padded` | coarse → reinforced |
| MEDIUM | `chainmail` | flint + copper…netherite |
| HEAVY | `platemail` | flint + copper…netherite |

Datapack `custom_trims` registers those ids (dev worlds under `paper-server/run/**/datapacks/custom_trims`).

## Local test (no PR required)

1. In assets repo, use the **working tree** under `pack/` (not an old zip).
2. Minecraft **1.21.11** → Options → Resource Packs → Open Pack Folder.
3. Either:
   - Symlink/copy the whole `pack/` folder into resourcepacks and enable it, **or**
   - Zip only the contents of `pack/` (`pack.mcmeta` at zip root).
4. **Disable every other pack** for the first test.
5. Creative world, no server needed for base check:
   - Put vanilla chainmail on an armor stand → must look normal (proves equipment base).
   - Put a VW chainmail piece with trim (from server or `/item` with trim components if you know them) on a stand → must show patterned armor, not magenta/black.
6. Fully quit client between pack edits (F3+T is not always enough after atlas changes).

### Isolating layers

| Test | Expected if pack is healthy |
|------|-----------------------------|
| Vanilla iron armor on stand | Normal iron (no pack dependency) |
| Vanilla chainmail on stand | Normal chain; if purple → equipment override broken |
| VW armor on stand | Patterned look; if purple → trim atlas/paths broken |
| VW armor in hotbar only | Custom icon; if good while stand is purple → icons OK, worn broken |

## Staging → production

Same as [contributing.md](contributing.md) / [ci-and-releases.md](ci-and-releases.md):

1. Local pack test green.
2. PR → CI `staging` release.
3. Staging server (Velocity already on `staging/pack.zip`) → rejoin after hash refresh.
4. Merge → `latest` for prod.

## Related files in this pack

| Path | Purpose |
|------|---------|
| `atlases/armor_trims.json` | Trim atlas (must use `trims/entity/…` paths) |
| `textures/trims/entity/humanoid/` | Custom + (via vanilla) pattern body |
| `textures/trims/entity/humanoid_leggings/` | Pattern legs |
| `textures/trims/color_palettes/` | Material colors |
| `textures/entity/equipment/…` | Worn base armor |
| `items/chainmail_*.json` | Inventory CMD only |

Do **not** reintroduce a custom `atlases/blocks.json` unless it is a full superset of vanilla — a minimal override wipes the entire blocks atlas.
