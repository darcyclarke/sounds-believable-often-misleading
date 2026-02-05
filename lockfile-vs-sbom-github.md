# Lockfile vs sbom-github.json

Quick facts:

- Lockfile: 146 `node_modules` entries (+ the local package link).
- SBOM (SPDX): 147 npm package entries; document name uses repo slug, not package name.

Key discrepancies (lockfile vs SBOM):

- Alias/tarball: lockfile installs `express` as `fastify@5.7.4` (tarball); SBOM only lists `fastify@5.7.4` and drops the `express` alias.
- npm alias: lockfile installs `chalk` as `colors@1.4.0`; SBOM only lists `colors@1.4.0`.
- git alias: lockfile installs `vue` as `set-proto@1.0.0` from git; SBOM only lists `set-proto@1.0.0` and loses the git source.
- override replacement: lockfile shows `yargs-parser` replaced by `minargs@2.1.0`; SBOM only lists `minargs@2.1.0`.
- file dependency: lockfile has `node_modules/lodash` as a `link: true` to `local@1.2.3`; SBOM lists `local@1.2.3` as a normal npm package and never mentions `lodash`.

Lost lockfile details:

- Direct dependency selectors in `packages[""].dependencies` (e.g., `next: canary`, `axios: *`, `yargs: >1`) are not represented.
- `resolved`, `integrity`, `os`, and `cpu` constraints in the lockfile are not carried over.
