# Lockfile vs sbom-npm.json

Quick facts:

- Lockfile `packages` map: 146 `node_modules/*` entries plus two non-node_modules entries (`""`, `local`).
- SBOM (CycloneDX): 117 `bom-ref` entries (116 components + 1 root).

Key discrepancies (lockfile vs SBOM) and resolution impact:

- Alias mismatch (high): SBOM `express` name + PURL `fastify@5.7.4`; lockfile shows `node_modules/express` with `name: fastify`. Without the alias/tarball spec, an install from SBOM cannot recreate the same tree shape.
- Alias mismatch (medium): SBOM `chalk` name + PURL `colors@1.4.0`; lockfile shows `node_modules/chalk` with `name: colors`. Package can be fetched, but alias name is lost.
- Alias mismatch (high): SBOM `vue` name + PURL `set-proto@1.0.0` (git); lockfile shows `node_modules/vue` with `name: set-proto` and a git URL. Git-sourced install cannot be reconstructed from a registry-only SBOM.
- Alias mismatch (medium): SBOM `yargs-parser` name + PURL `minargs@2.1.0`; lockfile shows the override replacing `yargs-parser` with `minargs`. Without the override rule, dependency resolution can differ.
- File dependency lost (high): lockfile has `node_modules/lodash` as a `link: true` to `local@1.2.3`; SBOM only lists `local@1.2.3` as if it were from npm. A link to a local folder cannot be reconstructed.
- Platform slice (high): lockfile includes many `@next/swc-*` and `@img/sharp-*` variants for multiple OS/CPU; SBOM only includes the `*-darwin-arm64` variants. Installs on other platforms cannot be reproduced from this SBOM.
- Project identity mismatch (low): lockfile name is `sounds-believable-often-misleading`, SBOM root component name is `test-sbom`. This is mostly metadata, but it can confuse automation that uses component identity.

Lost lockfile details (why SBOM cannot reproduce the lockfile):

- Direct dependency selectors in `packages[""].dependencies` (e.g., `next: canary`, `axios: *`, `yargs: >1`, tarball URL for `express`) are not represented.
- Lockfile `resolved` and `integrity` fields are not preserved verbatim (SBOM uses distribution URLs and SHA-512 hashes instead).
- `os`/`cpu` constraints and `link: true` semantics are not captured.
