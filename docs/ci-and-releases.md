# CI and releases

## Workflow

File: `.github/workflows/packsquash.yml`

On **every push** to any branch:

1. Checkout the repository.
2. Run [PackSquash](https://github.com/ComunidadAylas/PackSquash) on the `pack/` directory.
3. Write optimised output to `/tmp/pack.zip`.

On push to **`main`** only:

4. Publish `/tmp/pack.zip` as a **pre-release** on GitHub Releases (`latest` tag, title "Latest Build").

## PackSquash settings

```toml
pack_directory = 'pack'
output_file_path = '/tmp/pack.zip'
zip_spec_conformance_level = 'disregard'
```

`disregard` allows non-standard zip layout that PackSquash produces for optimised packs. Do not change unless you understand the downstream impact on how servers/clients load the archive.

## Getting a build

| Method | When to use |
|--------|-------------|
| GitHub Releases (`latest`) | Server deployment, QA, players |
| Local `pack/` folder | Active development — no optimisation, instant iteration |

There is no Gradle or npm build in this repo. Local testing does not require PackSquash.

## Optional: local PackSquash

Install [PackSquash](https://github.com/ComunidadAylas/PackSquash) and run against `pack/` to preview compression and catch pack errors before pushing:

```powershell
# Example — adjust path to your PackSquash binary
packsquash --config packsquash.toml
```

A project-local `packsquash.toml` is not checked in yet; CI config is inline in the workflow file. Add a local config if you optimise packs frequently.

## CI failures

Common causes:

| Symptom | Likely cause |
|---------|--------------|
| PackSquash parse error | Invalid JSON under `pack/` |
| Missing texture | Model references a PNG that does not exist |
| Workflow permission error | GitHub token / release action config (org settings) |

Fix the underlying asset or workflow issue and push again — the workflow runs on every push.

## Versioning

The pack does not use semver tags per release today. `main` publishes a rolling `latest` pre-release artifact. Record the commit SHA or release timestamp when deploying to a server so you can roll back.

Bump `pack/pack.mcmeta` format fields when raising the minimum Minecraft version — commit as `build(pack): bump pack format to …`.
