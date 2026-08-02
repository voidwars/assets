# Contributing

## Workflow (mandatory)

```
local → PR → staging channel → QA on staging server → merge → latest (production)
```

| Step | What you do | Channel |
|------|-------------|---------|
| 1 | Branch from `main` | — |
| 2 | Edit under `pack/` (see [adding-assets.md](adding-assets.md)) | — |
| 3 | Test **locally** in Minecraft **1.21.11** | — |
| 4 | Open PR; CI must pass | Publishes **`staging`** (if `pack/**` changed) |
| 5 | QA on **staging server** (not prod) | Still `staging` only |
| 6 | Merge PR **after** staging QA | Publishes **`latest`** → production |

Details, hard rules, and “prod looks like staging” debugging: [ci-and-releases.md](ci-and-releases.md).

### Hotfixes

Same steps. Local + staging QA are still required. There is no emergency path that writes `latest` without a merge.

### Do not

- Merge a pack PR that has not been seen on the staging server.
- Point production Velocity at the `staging` release URL.
- Open multiple competing pack PRs when both need staging QA (`staging` tag is singular).
- Push pack changes straight to `main` (no release is created; and it bypasses review).

New CMD IDs require a matching entry in voidwars-platform `VoidWarsModelData` — link both PRs.

## Pull requests

- One asset batch per PR when possible.
- Include before/after screenshots for visual changes.
- Use the PR template checklist end-to-end.
- Prefer **one open pack PR** at a time for staging-server QA.

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
| Worn armor / trims | atlas + `trims/entity/…` textures (see [armor-and-trims.md](armor-and-trims.md)) |

Edit **`pack/`** for anything that ships in the zip. Docs under `docs/` do not republish channels by themselves.

## JSON rules

- Standard JSON only — no comments, no trailing commas.
- Minecraft item models: `"model": "item/flint_sword"`
- ModelEngine models: `"model": "modelengine:vw_mimic/tongue1"`
