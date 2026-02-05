# Lockfile vs sbom-github.json

## At a glance

- Lockfile `packages` map: 146 `node_modules/*` entries plus two non-node_modules entries (`""`, `local`).
- SBOM (SPDX): 147 npm package entries; document name is `com.github.darcyclarke/sounds-believable-often-misleading`.
- Reproducibility: SBOM cannot recreate the lockfile or `node_modules` graph as-installed.

## Resolution-critical mismatches (wrong package/source => wrong subgraph)

- `vue` resolves to a git-sourced `set-proto@1.0.0` in the lockfile; SBOM drops the git URL. A registry install is not equivalent.
- `lodash` is a `link: true` file dependency to `local@1.2.3`; SBOM lists only `local@1.2.3` as if it were from npm.
- `express` resolves to `fastify@5.7.4` from a tarball; SBOM lists `fastify@5.7.4` but loses the tarball spec and alias name.

## Resolution-shape mismatches (package may be same, tree still differs)

- `chalk` is an alias for `colors@1.4.0`; SBOM lists `colors@1.4.0` only, so the alias name and path are lost.
- `yargs-parser` is overridden to `minargs@2.1.0`; SBOM lists `minargs@2.1.0` but not the override rule, so resolution can differ.

## Spec loss (mostly affects future updates/rebuilds)

- `downloadLocation` is `NOASSERTION`, so lockfile `resolved` URLs and `integrity` values are not represented.
- `optional`/`peer` flags and `os`/`cpu` constraints from the lockfile are not captured.
- Direct dependency selectors in `packages[""].dependencies` (e.g., `next: canary`, `axios: *`, `yargs: >1`) are not represented.

## Why this matters

- If any resolved package identity is wrong, its entire transitive dependency subtree diverges from the lockfile.
