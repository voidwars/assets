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

Production uses `latest` by default. Staging must set:

```
RESOURCE_PACK_URL=https://github.com/voidwars/assets/releases/download/staging/pack.zip
```

The proxy polls the URL every ~30 seconds and pushes the pack when the SHA-1 hash changes.

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
