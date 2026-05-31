# Etheria Resource Pack

Custom Minecraft resource pack for **Etheria's Last Stand** (Voidwars). Provides item models, textures, GUI icons, armor trims, shaders, and ModelEngine mob assets used by the game server.

Target: **Minecraft 1.21.4** (pack format 75).

## Repository layout

| Path | Purpose |
|------|---------|
| `pack/` | **Authoritative pack** — CI builds and releases from here |
| `pack/assets/minecraft/` | Vanilla namespace: items, models, textures, lang, shaders |
| `pack/assets/modelengine/` | ModelEngine mob models and textures |
| `.github/workflows/` | PackSquash CI and GitHub Releases |

## Quick start

1. Clone the repo.
2. Edit assets under `pack/` only.
3. Test locally by pointing Minecraft at `pack/` as a resource pack folder, or download the latest build from [GitHub Releases](https://github.com/voidwars/assets/releases).
4. Commit using [Conventional Commits](docs/contributing.md).

No build step is required for local testing — open `pack/` directly in the client. CI optimises the pack with [PackSquash](https://github.com/ComunidadAylas/PackSquash) on every push.

## Documentation

| Topic | Guide |
|-------|-------|
| Folder structure and namespaces | [docs/structure.md](docs/structure.md) |
| Custom model data (CMD) IDs and host items | [docs/custom-model-data.md](docs/custom-model-data.md) |
| Adding a new item or icon | [docs/adding-assets.md](docs/adding-assets.md) |
| Commits and pull requests | [docs/contributing.md](docs/contributing.md) |
| CI, PackSquash, and releases | [docs/ci-and-releases.md](docs/ci-and-releases.md) |

## Related repos

- **voidwars-platform** — game server; assigns `custom_model_data` values via `VoidWarsModelData` in `services/paper-server/`. Any new CMD ID must be added there **and** in the matching `pack/assets/minecraft/items/<host>.json` entry.

## Pack metadata

```json
// pack/pack.mcmeta
{
  "pack": {
    "min_format": 75,
    "max_format": 75,
    "description": "Etheria's Last Stand"
  }
}
```

Bump `min_format` / `max_format` together when targeting a new Minecraft version.
