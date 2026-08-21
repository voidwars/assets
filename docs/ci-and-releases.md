# CI and releases

One git branch (`main`). Three pack objects (GitHub release **tags**). Velocity pins each lane to one URL.

```
local test → PR (pack/**) → pack-pr-<N>
           → merge → staging tag → QA on :25566
           → Promote pack → latest tag → prod :25565
```

Code previews (`play.skyfire.network:26200+PR`) use the **staging** pack by default so a Java PR does not pick up an unfinished model. `pack-pr-<N>` is for downloading or later wiring.

## Channels

| Channel | Tag | URL | When it updates |
|---------|-----|-----|-----------------|
| PR | `pack-pr-<N>` | `…/releases/download/pack-pr-<N>/pack.zip` | PR open/push touching `pack/**` |
| Staging | `staging` | `…/releases/download/staging/pack.zip` | PR **merged** to `main` |
| Production | `latest` | `…/releases/download/latest/pack.zip` | **Promote pack** workflow only |

## Hard rules

1. Prod Velocity uses the `latest` URL only. Staging and code previews use `staging`.
2. Merge does **not** publish prod. Operators: Actions → **Promote pack to prod** → type `promote`.
3. CI green ≠ in-game QA. Play `:25566` before promoting.
4. Docs-only PRs skip the workflow (`paths` filter).

## Velocity wiring

| Environment | `resourcePackUrl` |
|-------------|-------------------|
| Preview | staging pack |
| Staging (`:25566`) | staging pack |
| Production (`:25565`) | latest pack |
