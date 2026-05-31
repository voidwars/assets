# Contributing

## Commit messages

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <imperative summary>
```

### Types

| Type | Use for |
|------|---------|
| `feat` | New or updated textures, models, GUI icons, mob models, lang |
| `fix` | Broken paths, missing textures, wrong CMD thresholds |
| `refactor` | Reorganisation or format migration without visual change |
| `build` | `pack.mcmeta`, PackSquash/CI, pack metadata |
| `chore` | Gitignore, bulk renames |
| `docs` | README and `docs/` only |

### Scopes

`items`, `models`, `textures`, `gui`, `skills`, `containers`, `modelengine`, `lang`, `shaders`, `pack`

Omit scope when a change spans multiple areas (e.g. new item = model + texture + item entry).

### Examples

```
feat(gui): add stash control icons for mailbox, trash, and pagination
feat(items): migrate stick CMD variants to items/stick.json
fix(textures): correct gui_trash UV mapping
docs: add pack structure and CMD reference guides
```

## Pull requests

1. Branch from `main`.
2. Keep PRs focused — one asset batch or one migration per PR when possible.
3. Include **before/after screenshots** for visual changes (inventory GUI is enough).
4. Note the matching **voidwars-platform PR** if CMD IDs were added server-side.
5. CI must pass (PackSquash optimisation — see [ci-and-releases.md](ci-and-releases.md)).

## What to commit together

| Change | Commit together |
|--------|-----------------|
| New item icon | `VoidWarsModelData` (platform) + item entry + model + texture |
| GUI icon | model + texture + host item entry |
| Format migration | all moved JSON for that host item |
| Docs only | `docs/` and/or `README.md` — type `docs` |

Do **not** edit outside `pack/` — it is not built or released.

## JSON hygiene

- Standard JSON only (no comments, no trailing commas).
- Use tabs or spaces consistently within a file (existing models vary; match the file you edit).
- Model references omit the namespace for minecraft items: `"model": "item/flint_sword"`.
- ModelEngine references use the namespace: `"model": "modelengine:vw_mimic/tongue1"`.
