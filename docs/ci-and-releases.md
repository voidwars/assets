# CI and releases

One branch (`main`). Two tags. Staging and every game preview share the same zip.

```
edit pack/ → merge (or push) to main → tag staging → :25566 and code previews
           → Promote pack → tag latest → :25565
```

No per-PR pack build. Nothing in the cluster reads `pack-pr-*`.

## Channels

| Channel | Tag | URL | When it updates |
|---------|-----|-----|-----------------|
| Staging + previews | `staging` | `…/releases/download/staging/pack.zip` | Push/merge to `main` touching `pack/**` |
| Production | `latest` | `…/releases/download/latest/pack.zip` | **Promote pack** only |

## Hard rules

1. Prod Velocity uses `latest` only. Staging and code previews use `staging`.
2. Merge does **not** publish prod. Actions → **Promote pack to prod** → type `promote`.
3. Play `:25566` before promoting.
4. Docs-only changes skip the pack job (`paths` filter).

## Velocity wiring

| Environment | `resourcePackUrl` |
|-------------|-------------------|
| Preview | staging pack |
| Staging (`:25566`) | staging pack |
| Production (`:25565`) | latest pack |
