# Etheria Resource Pack

Custom Minecraft resource pack for **Etheria's Last Stand** (Voidwars): item models, textures, GUI icons, armor trims, shaders, and ModelEngine mob assets.

**Target:** Minecraft **1.21.11** · pack format **75** (`pack/pack.mcmeta`)

## Workflow

```
branch → PR → staging release → QA → merge PR → latest release
```

| | When | Download |
|--|------|----------|
| **Staging** | PR opened or updated | [staging/pack.zip](https://github.com/voidwars/assets/releases/download/staging/pack.zip) |
| **Production** | PR merged to `main` | [latest/pack.zip](https://github.com/voidwars/assets/releases/download/latest/pack.zip) |

1. Edit files under `pack/` only.
2. Test locally — load `pack/` as a resource pack in Minecraft 1.21.11.
3. Open a PR → CI publishes **staging**.
4. QA on the staging server, then merge → CI publishes **latest**.

Pushing directly to `main` does not create a release. See [docs/ci-and-releases.md](docs/ci-and-releases.md).

## Layout

| Path | Purpose |
|------|---------|
| `pack/` | Authoritative pack — CI builds from here |
| `pack/assets/minecraft/` | Items, models, textures, lang, shaders |
| `pack/assets/modelengine/` | ModelEngine mob models and textures |

## Documentation

| Topic | Guide |
|-------|-------|
| Folder structure | [docs/structure.md](docs/structure.md) |
| Custom model data (CMD) | [docs/custom-model-data.md](docs/custom-model-data.md) |
| Adding an item or icon | [docs/adding-assets.md](docs/adding-assets.md) |
| **Armor worn look + trims** | [docs/armor-and-trims.md](docs/armor-and-trims.md) |
| Commits and PRs | [docs/contributing.md](docs/contributing.md) |
| CI and releases | [docs/ci-and-releases.md](docs/ci-and-releases.md) |

## Related repos

**voidwars-platform** assigns `custom_model_data` via `VoidWarsModelData` (`services/paper-server/`). New CMD IDs must be added there and in the matching `pack/assets/minecraft/items/<host>.json` entry.

## Servers

| Environment | Velocity `RESOURCE_PACK_URL` |
|-------------|------------------------------|
| Staging | `https://github.com/voidwars/assets/releases/download/staging/pack.zip` |
| Production | `https://github.com/voidwars/assets/releases/download/latest/pack.zip` |

These are **tag download URLs**. Whether the GitHub release is marked pre-release or full does not change the download path. CI keeps `staging` as pre-release and `latest` as a full production release for UI clarity only.
