# Lockfile vs sbom-npm.json

## At a glance

- Lockfile `packages` map: 146 `node_modules/*` entries plus two non-node_modules entries (`""`, `local`).
- SBOM (CycloneDX): 117 `bom-ref` entries (116 components + 1 root).
- Reproducibility: SBOM cannot recreate the lockfile or `node_modules` graph as-installed.

## Resolution-critical mismatches (wrong package/source => wrong subgraph)

- Root component PURL uses `pkg:npm/sounds-believable-often-misleading@1.0.0`, but the package is not published to npm. Install-from-SBOM fails or fetches the wrong thing.
- `vue` resolves to a git-sourced `set-proto@1.0.0`; SBOM drops the git URL, so the source is wrong.
- `lodash` is a `link: true` file dependency to `local@1.2.3`; SBOM lists only `local@1.2.3` as if it were from npm.
- Platform slice: lockfile includes many `@next/swc-*` and `@img/sharp-*` variants for multiple OS/CPU; SBOM only includes the `*-darwin-arm64` variants. Other platforms cannot be reproduced.

## Resolution-shape mismatches (package may be same, tree still differs)

- `express` resolves to `fastify@5.7.4` from a tarball; SBOM shows `express` with PURL `fastify@5.7.4` but loses the tarball spec and alias name.
- `chalk` is an alias for `colors@1.4.0`; SBOM lists `colors@1.4.0` only, so the alias name and path are lost.
- `yargs-parser` is overridden to `minargs@2.1.0`; SBOM lists `minargs@2.1.0` but not the override rule, so resolution can differ.

## Spec loss (mostly affects future updates/rebuilds)

- Direct dependency selectors in `packages[""].dependencies` (e.g., `next: canary`, `axios: *`, `yargs: >1`, tarball URL for `express`) are not represented.
- Lockfile `resolved` and `integrity` fields are not preserved verbatim (SBOM uses distribution URLs and SHA-512 hashes instead).
- `os`/`cpu` constraints and `link: true` semantics are not captured.

## Why this matters

- If any resolved package identity is wrong, its entire transitive dependency subtree diverges from the lockfile.
