# Lockfile vs sbom-npm.json

Quick facts:

- Lockfile: 146 `node_modules` entries (+ the local package link).
- SBOM (CycloneDX): 117 components (including the root component).

Key discrepancies (lockfile vs SBOM):

- Alias mismatch: SBOM `express` name + PURL `fastify@5.7.4`; lockfile shows `node_modules/express` with `name: fastify`.
- Alias mismatch: SBOM `chalk` name + PURL `colors@1.4.0`; lockfile shows `node_modules/chalk` with `name: colors`.
- Alias mismatch: SBOM `vue` name + PURL `set-proto@1.0.0` (git); lockfile shows `node_modules/vue` with `name: set-proto` and a git URL.
- Alias mismatch: SBOM `yargs-parser` name + PURL `minargs@2.1.0`; lockfile shows the override replacing `yargs-parser` with `minargs`.
- File dependency lost: lockfile has `node_modules/lodash` as a `link: true` to `local@1.2.3`; SBOM only lists `local@1.2.3` as if it were from npm.
- Project identity mismatch: lockfile name is `sounds-believable-often-misleading`, SBOM root component name is `test-sbom`.

Lost lockfile details:

- Direct dependency selectors in `packages[""].dependencies` (e.g., `next: canary`, `axios: *`, `yargs: >1`, tarball URL for `express`) are not represented.
- `resolved`, `integrity`, `os`, and `cpu` constraints in the lockfile are not carried over.
