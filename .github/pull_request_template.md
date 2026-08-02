## Summary

<!-- What changed and why. Note if this is a hotfix. -->

## Screenshots

<!-- Required for visual changes (inventory + worn/armor stand when relevant) -->

## Checklist

### Before opening / while in review

- [ ] Shippable edits under `pack/` (docs-only PRs do not publish pack channels)
- [ ] JSON valid — no trailing commas; model and texture paths resolve
- [ ] CMD IDs match voidwars-platform `VoidWarsModelData` (link platform PR if applicable)
- [ ] **Tested locally** in Minecraft 1.21.11 (not only CI green)

### Staging gate (required before merge)

- [ ] CI job **Build and publish staging** succeeded
- [ ] Joined **staging** server (not prod) and confirmed pack hash refreshed
- [ ] QA on staging: icons, worn armor / armor stands, anything this PR touches
- [ ] No other open pack PR is fighting for the singular `staging` release tag (or you coordinated)

### Production (only when merging)

- [ ] Staging QA is good — **merge is the production publish**
- [ ] After merge: confirm Actions **Build and publish production** and prod Velocity log shows `…/latest/pack.zip` + new hash

**Do not merge** untested pack changes. **Do not** point prod Velocity at `staging/pack.zip`.
