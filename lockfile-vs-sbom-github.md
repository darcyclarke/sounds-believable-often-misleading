# Lockfile vs sbom-github.json

Quick facts:

- Lockfile `packages` map: 146 `node_modules/*` entries plus two non-node_modules entries (`""`, `local`).
- SBOM (SPDX): 147 npm package entries; document name is `com.github.darcyclarke/sounds-believable-often-misleading`.

Key discrepancies (lockfile vs SBOM) and resolution impact:

- Tarball under alias name (high): dependency `express` resolves to `fastify@5.7.4` (tarball); SBOM lists `fastify@5.7.4` only. Installing from SBOM cannot recreate `node_modules/express` without the alias or tarball spec, so the tree shape differs.
- npm alias (medium): lockfile installs `chalk` as `colors@1.4.0`; SBOM only lists `colors@1.4.0`. You can fetch the same package, but the alias name (`chalk`) is lost, so the resolved tree is not identical.
- git alias (high): lockfile installs `vue` as `set-proto@1.0.0` from git; SBOM lists `set-proto@1.0.0` without the git URL. A registry install is not equivalent to the git-sourced dependency, so the tree may differ.
- override replacement (medium): lockfile shows `yargs-parser` replaced by `minargs@2.1.0`; SBOM only lists `minargs@2.1.0`. Without the override rule, an install can resolve `yargs-parser` instead.
- file dependency (high): lockfile has `node_modules/lodash` as a `link: true` to `local@1.2.3`; SBOM lists `local@1.2.3` as a normal npm package and never mentions `lodash`. A lockfile-style link cannot be reconstructed from the SBOM.

Lost lockfile details (why SBOM cannot reproduce the lockfile):

- `downloadLocation` is `NOASSERTION`, so lockfile `resolved` URLs and `integrity` values are not represented.
- `optional`/`peer` flags and `os`/`cpu` constraints from the lockfile are not captured.
- Direct dependency selectors in `packages[""].dependencies` (e.g., `next: canary`, `axios: *`, `yargs: >1`) are not represented.
