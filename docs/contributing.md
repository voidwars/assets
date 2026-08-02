# Contributing

## Workflow

| Step | What you do | Release |
|------|-------------|---------|
| 1 | Branch from `main` (`feat/my-icons`) | — |
| 2 | Edit under `pack/` — see [adding-assets.md](adding-assets.md) | — |
| 3 | Test locally in Minecraft **1.21.11** | — |
| 4 | Open PR (CI must pass) | **`staging`** |
| 5 | QA on staging server | — |
| 6 | Merge PR | **`latest`** (production) |

New CMD IDs require a matching entry in voidwars-platform `VoidWarsModelData` — link both PRs.

## Pull requests

- One asset batch per PR when possible.
- Include before/after screenshots for visual changes.
- Use the PR template checklist.
- Do not push asset changes directly to `main`.

## Commit messages

[Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <imperative summary>
```

| Type | Use for |
|------|---------|
| `feat` | New or updated textures, models, GUI icons, mob models, lang |
| `fix` | Broken paths, missing textures, wrong CMD thresholds |
| `refactor` | Reorganisation or format migration without visual change |
| `build` | `pack.mcmeta`, CI workflow, pack metadata |
| `chore` | Gitignore, bulk renames |
| `docs` | README and `docs/` only |

Scopes: `items`, `models`, `textures`, `gui`, `skills`, `containers`, `modelengine`, `lang`, `shaders`, `armor`, `trims`, `pack`

```
feat(gui): add stash control icons for mailbox, trash, and pagination
fix(textures): correct gui_trash UV mapping
build(pack): bump pack format to 75 for 1.21.11
```

## What to commit together

| Change | Include in same PR |
|--------|-------------------|
| New item icon | platform `VoidWarsModelData` + item entry + model + texture |
| GUI icon | model + texture + host item entry |
| Format migration | all JSON moved for that host item |

Edit **`pack/`** only — nothing outside it is built or released.

## JSON rules

- Standard JSON only — no comments, no trailing commas.
- Minecraft item models: `"model": "item/flint_sword"`
- ModelEngine models: `"model": "modelengine:vw_mimic/tongue1"`
