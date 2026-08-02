# CI and releases

This repo has **one product branch** (`main`) and **two pack channels** (GitHub release **tags**, not git branches). Velocity pins each environment to exactly one channel.

## Intended train (do not skip)

```
local test → PR (pack/**) → staging channel → QA on staging server → merge → latest channel → prod
```

| Gate | Who | Required before next step |
|------|-----|---------------------------|
| Local | Author | Pack loads in Minecraft **1.21.11**; no missing textures |
| PR + CI | Author / bot | PackSquash green; **`staging`** release rewritten from the PR head |
| Staging QA | Author / operator | Play on staging proxy (`RESOURCE_PACK_URL` → `staging` tag); armor stands, icons, etc. |
| Merge | Reviewer | **Only after staging QA.** This is the production publish button |
| Prod observe | Operator | Prod Velocity hash log updates to new `latest` (~30s); rejoin if client cached old zip |

**Hotfix:** same train, shorter QA. There is **no** “push straight to `latest`” path. That is intentional.

## Channels

| Channel | GitHub tag | URL | When it updates |
|---------|------------|-----|-----------------|
| **Staging** | `staging` (prerelease) | https://github.com/voidwars/assets/releases/download/staging/pack.zip | PR **opened** or **updated**, and the PR touches `pack/**` (or this workflow) |
| **Production** | `latest` (full release) | https://github.com/voidwars/assets/releases/download/latest/pack.zip | PR **merged** to `main` (same path filter) |

| Event | Staging tag | Latest tag |
|-------|-------------|------------|
| PR open / push (pack changes) | Rebuild | Unchanged |
| PR closed **without** merge | Unchanged | Unchanged |
| PR **merged** | Unchanged by merge job* | Rebuild from merge commit |
| Docs-only PR | Workflow **skipped** | Workflow **skipped** |
| Direct push to `main` | Nothing | Nothing |

\*A later pack PR may overwrite the `staging` tag; that does not rewrite `latest`.

## Hard rules

1. **Production Velocity must use the `latest` URL only.**  
   Staging Velocity must use the `staging` URL only.  
   Never set prod `resourcePackUrl` to `…/staging/pack.zip`.
2. **Merge is the only way to update production.**  
   An open PR can never change `latest`. If prod looks “fixed” while a pack PR is still open, you are either on the staging server, looking at a client cache, or something else is wrong — check Velocity logs for URL + SHA-1.
3. **One pack PR in flight** for shared QA.  
   The `staging` tag is **singular**. The last pack PR that ran CI wins. Do not parallelize unrelated visual PRs if both need staging-server QA.
4. **Do not merge untested pack PRs.**  
   CI green ≠ staging QA. Purple armor, bad icons, etc. only show up in-game.
5. **Docs-only changes** do not rebuild either channel (workflow `paths` filter). Safe to merge docs without touching live packs.

## Velocity wiring (deployments)

| Environment | Namespace | Typical host port | `resourcePackUrl` |
|-------------|-----------|-------------------|-------------------|
| Staging | `voidwars-staging` | 25566 | `…/releases/download/staging/pack.zip` |
| Production | `voidwars` | 25565 | `…/releases/download/latest/pack.zip` |

Proxy polls the URL ~every 30s, SHA-1s the zip, and offers the pack when the hash changes. URLs are **tag download** paths, not `GET /repos/.../releases/latest`. Prerelease vs full release does not affect the download URL.

### Prove which pack a proxy serves

```text
# logs (ResourcePackService)
Resource pack URL: https://github.com/voidwars/assets/releases/download/<channel>/pack.zip
Updated resource pack info with hash: <sha1>
```

| Expect | Staging | Production |
|--------|---------|------------|
| URL path | `/staging/pack.zip` | `/latest/pack.zip` |
| Hash | Matches current staging release | Matches current latest release |

Compare zip SHA-1 to GitHub release assets if needed. They **must** differ when a pack PR is open and unmerged (staging ahead of latest).

## CI workflow file

`.github/workflows/packsquash.yml`

| Job | Condition | Publishes |
|-----|-----------|-----------|
| `staging` | PR open/sync/reopen (not closed) | tag `staging`, prerelease |
| `production` | PR closed **and** `merged == true` | tag `latest`, full release |

PackSquash options:

```toml
pack_directory = 'pack'
output_file_path = '/tmp/pack.zip'
zip_spec_conformance_level = 'disregard'
```

Only content under `pack/` is shipped.

## Pack format

| Minecraft | `pack_format` | `min_format` / `max_format` |
|-----------|---------------|-------------------------------|
| **1.21.11** (current) | **75** | **75** |

## Operator checklist

### Ship a pack change (normal or hotfix)

1. Branch from `main`; edit `pack/` (and docs if needed).
2. Test **locally** with `pack/` as a resource pack (see [armor-and-trims.md](armor-and-trims.md) for worn-armor isolation).
3. Open PR → wait for **Build and publish staging**.
4. Join **staging** server; force client pack refresh (full quit if hash stuck).
5. QA; if bad, push more commits (staging tag updates again) — **do not merge**.
6. Merge only when staging QA is good → **Build and publish production**.
7. Confirm prod Velocity log shows `latest` URL and new hash; rejoin prod.

### If production looks like staging

1. Check prod Velocity env: must be `…/latest/pack.zip`, not `…/staging/…`.
2. Compare release titles/dates on GitHub (`staging` vs `latest`).
3. Compare SHA-1 in Velocity logs to local download of each URL.
4. Clear **client** pack cache (full game quit); client may still hold a previous zip.

### If latest is stale after a merge

1. Confirm the merge PR actually changed `pack/**` (docs-only merges do not publish).
2. Confirm the **production** job ran on the merge (Actions tab).
3. Confirm Workflow permissions allow release write (repo Settings → Actions → General).

## Troubleshooting

| Symptom | Likely cause |
|---------|--------------|
| PackSquash parse error | Invalid JSON under `pack/` |
| Missing texture (item icon) | Model references a missing PNG under `textures/item/` |
| Purple/black worn armor, icons OK | Trim/equipment paths — [armor-and-trims.md](armor-and-trims.md) |
| Staging good, prod still old | PR not merged, or production job failed, or client cache |
| Prod and staging same hash while PR open | Unexpected — verify URLs; prod must not use staging tag |
| Docs PR did not update staging | Intended — `paths` filter skips non-pack PRs |
| Release step fails | Actions → General → Workflow permissions: read/write |
