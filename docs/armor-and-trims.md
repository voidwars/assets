# Armor and trims

Voidwars armor uses **two independent client channels**:

| Channel | What you see | Driven by |
|---------|--------------|-----------|
| **Item icon** | Inventory / hotbar / ground | `items/chainmail_*.json` CMD → Blockbench models under `models/item/*_padded_*` / `*_chainmail_*` / etc. |
| **Worn model** | On players, mobs, armor stands | Vanilla equipment layers + **armor trim** (pattern × material) |

Icons can look perfect while worn armor is purple/black — that is expected if only the item pipeline is healthy.

## How the game builds worn armor (server)

Paper applies a trim in `ArmorStats.trimArmor` via `ArmorConsts`:

| Armor type | Trim **pattern** (datapack id) | Tier colors via trim **material** |
|------------|--------------------------------|-------------------------------------|
| LIGHT | `padded` | `coarse` … `reinforced` (custom) |
| MEDIUM | `chainmail` | `flint` + vanilla copper/iron/gold/diamond/netherite |
| HEAVY | `platemail` | same metal materials as medium |

Registry keys live in platform `VoidWarsDatapack` (`minecraft:padded`, `minecraft:coarse`, …). Those keys must exist in the **server datapack** *and* in the resource pack atlas / textures.

## Pack layout (1.21.2+ / pack format 75)

### Worn base armor textures (equipment)

Since **1.21.2**, equipment textures live under `textures/entity/equipment/`, **not** only under the legacy `textures/models/armor/` path.

| Legacy (pre-1.21.2) | Current (required for 1.21.11 worn look) |
|---------------------|------------------------------------------|
| `textures/models/armor/<mat>_layer_1.png` | `textures/entity/equipment/humanoid/<mat>.png` |
| `textures/models/armor/<mat>_layer_2.png` | `textures/entity/equipment/humanoid_leggings/<mat>.png` |
| `leather_layer_1_overlay.png` | `humanoid/leather_overlay.png` |
| `leather_layer_2_overlay.png` | `humanoid_leggings/leather_overlay.png` |

This pack ships **both**: legacy copies remain for tooling/reference; the **equipment** copies are what the 1.21.11 client uses when rendering worn leather/chainmail.

Materials we override today: `leather`, `chainmail`.

### Armor trims

| Path | Role |
|------|------|
| `textures/trims/models/armor/<pattern>.png` | Pattern masks (body) |
| `textures/trims/models/armor/<pattern>_leggings.png` | Pattern masks (legs) |
| `textures/trims/color_palettes/<material>.png` | Color ramps for custom materials |
| `atlases/armor_trims.json` | `paletted_permutations` — wires patterns × materials into the trim atlas |
| `lang/en_us.json` | Display names for custom patterns/materials |

Custom patterns present: `padded`, `chainmail`, `platemail`.  
Custom materials present: `coarse`, `rugged`, `steeled`, `mighty`, `robust`, `reinforced`, `flint` (+ overrides for some vanilla palettes like `gold` / `netherite`).

Vanilla patterns (coast, sentry, …) are still listed in the atlas so vanilla trims keep working; textures resolve from the default assets unless overridden.

### Inventory icons (not worn)

CMD hosts `chainmail_helmet|chestplate|leggings|boots` → models like `item/coarse_padded_chestplate`. Documented in [custom-model-data.md](custom-model-data.md) (1501–1518 band).

## Symptom: purple / black worn armor

Minecraft missing-texture colors (magenta + black). Typical Voidwars cases:

| Observation | Likely cause |
|-------------|--------------|
| Icons OK, worn armor purple/black on players **and** mobs | Equipment textures missing or still only on legacy `models/armor` paths |
| Only trims wrong; base armor OK | `atlases/armor_trims.json` / pattern PNG / palette PNG mismatch |
| Only custom patterns wrong | Missing `trims/models/armor/padded.png` (etc.) or not listed in atlas |
| Only custom materials wrong | Missing `trims/color_palettes/<id>.png` or not listed under `permutations` |
| Unarmored player skin wrong | Separate issue — `shaders/core/rendertype_entity_translucent*` (dismemberment), not armor |
| Staging OK, prod wrong | Stale `latest` release — see [ci-and-releases.md](ci-and-releases.md) |

## Local verification checklist

1. Load **only** this pack (or server pack) on Minecraft **1.21.11**.
2. Wear vanilla leather + chainmail — base layers must not be purple.
3. Wear Voidwars loot armor (or apply trims via smithing/debug) for `padded` / `chainmail` / `platemail` × a custom material.
4. Confirm inventory icon still matches CMD model.
5. Check third-person + another entity (zombie in armor / armor stand).

## Staging → production (assets only)

Same as [contributing.md](contributing.md) / [ci-and-releases.md](ci-and-releases.md):

1. Branch from `main`, edit under `pack/` only.
2. Open PR → CI publishes **`staging`** pack.zip.
3. Staging Velocity already points at `…/releases/download/staging/pack.zip` — reconnect after hash refresh (~30s), or fully quit the client to drop cache.
4. QA worn armor + icons on **staging** (`:25566`).
5. Merge PR → CI publishes **`latest`**.
6. Production Velocity uses `…/latest/pack.zip` — no deployments change required for pack-only fixes; wait for proxy hash update, then rejoin prod.

## Related code (not in this repo)

| Repo | File | Role |
|------|------|------|
| platform / platform-staging | `game/ArmorConsts.java` | Pattern + material per tier/type |
| platform / platform-staging | `game/VoidWarsDatapack.java` | Registry lookups for custom trims |
| platform / platform-staging | `tags/combat/ArmorStats.java` | Applies trim on item create |
| platform / platform-staging | `VoidWarsModelData` + chainmail hosts | Inventory CMD icons |

Pack PRs do **not** need a platform PR unless you add new pattern/material **ids** (then datapack + `VoidWarsDatapack` + atlas + textures must land together).
