# CI and releases

## Channels

One git branch (`main`). Two GitHub **release tags** (not git branches):

| Tag | URL | Updates when |
|-----|-----|--------------|
| `staging` | https://github.com/voidwars/assets/releases/download/staging/pack.zip | PR **opened** or **updated** |
| `latest` | https://github.com/voidwars/assets/releases/download/latest/pack.zip | PR **merged** to `main` |

Direct pushes to `main` without a PR do not publish a release.

## CI workflow

File: `.github/workflows/packsquash.yml`

| Event | Action |
|-------|--------|
| PR opened / new commits | PackSquash build → publish **`staging`** |
| PR merged | PackSquash build → publish **`latest`** |
| PR closed without merge | Nothing |

PackSquash config:

```toml
pack_directory = 'pack'
output_file_path = '/tmp/pack.zip'
zip_spec_conformance_level = 'disregard'
```

## Velocity proxies

Deployments set `resourcePackUrl` per env (injected as `RESOURCE_PACK_URL`):

```
# staging
RESOURCE_PACK_URL=https://github.com/voidwars/assets/releases/download/staging/pack.zip
# production
RESOURCE_PACK_URL=https://github.com/voidwars/assets/releases/download/latest/pack.zip
```

These are fixed **tag** download URLs, not `GET /repos/.../releases/latest`. A pre-release still downloads at `/releases/download/<tag>/pack.zip`. The proxy polls every ~30s, SHA-1s the zip, and offers the pack to players when the hash is new (not on every hub↔raid hop).

## Pack format

| Minecraft | `pack_format` | `min_format` / `max_format` |
|-----------|---------------|-------------------------------|
| **1.21.11** (current) | **75** | **75** |

Set all three fields when bumping versions. PackSquash requires `pack_format`; Minecraft 1.21.9+ clients use `min_format` / `max_format`.

## Troubleshooting

| Symptom | Likely cause |
|---------|--------------|
| PackSquash parse error | Invalid JSON under `pack/` |
| Missing texture | Model references a PNG that does not exist |
| Release step fails | Repo **Settings → Actions → General → Workflow permissions** must allow read/write |
