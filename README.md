# Etheria Resource Pack

Custom Minecraft resource pack for **Etheria's Last Stand** (Voidwars): item models, textures, GUI icons, armor trims, shaders, and ModelEngine mob assets.

**Target:** Minecraft **1.21.11** · pack format **75** (`pack/pack.mcmeta`)

## Workflow (required)

```
local test → PR → staging release → QA on staging → merge → latest (production)
```

| Channel | When | Download |
|---------|------|----------|
| **Staging** | PR opened/updated (`pack/**` changes) | [staging/pack.zip](https://github.com/voidwars/assets/releases/download/staging/pack.zip) |
| **Production** | PR **merged** to `main` | [latest/pack.zip](https://github.com/voidwars/assets/releases/download/latest/pack.zip) |

1. Edit files under `pack/` only (for shippable content).
2. Test **locally** in Minecraft 1.21.11.
3. Open a PR → CI publishes **`staging`** (pack changes only; docs-only PRs do not).
4. QA on the **staging** server — do not merge yet.
5. Merge → CI publishes **`latest`**. Production picks it up via Velocity (no “copy staging URL to prod”).

**Hotfixes use the same gates.** There is no direct publish to production.  
**Never** point production Velocity at the staging URL.  
Details: [docs/ci-and-releases.md](docs/ci-and-releases.md).

## Layout

| Path | Purpose |
|------|---------|
| `pack/` | Authoritative pack — CI builds from here only |
| `pack/assets/minecraft/` | Items, models, textures, lang, shaders, trim atlas |
| `pack/assets/modelengine/` | ModelEngine mob models and textures |

## Documentation

| Topic | Guide |
|-------|-------|
| **CI, channels, prod vs staging gates** | [docs/ci-and-releases.md](docs/ci-and-releases.md) |
| Commits and PRs | [docs/contributing.md](docs/contributing.md) |
| Folder structure | [docs/structure.md](docs/structure.md) |
| Custom model data (CMD) | [docs/custom-model-data.md](docs/custom-model-data.md) |
| Adding an item or icon | [docs/adding-assets.md](docs/adding-assets.md) |
| Armor on stands / worn trims | [docs/armor-and-trims.md](docs/armor-and-trims.md) |

## Related repos

**voidwars-platform** assigns `custom_model_data` via `VoidWarsModelData` (`services/paper-server/`). New CMD IDs must be added there and in the matching `pack/assets/minecraft/items/<host>.json` entry.

## Servers (Velocity)

| Environment | Must use |
|-------------|----------|
| Staging (`voidwars-staging`, port **25566**) | `…/releases/download/staging/pack.zip` |
| Production (`voidwars`, port **25565**) | `…/releases/download/latest/pack.zip` |

These are **tag** download URLs. Confirm in Velocity logs (`Resource pack URL` + hash) — open pack PRs update **staging** only until merge.
