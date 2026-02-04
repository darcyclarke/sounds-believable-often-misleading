# sounds-believable-often-misleading

This repo exists to show how SBOMs can be junk in practice: they look authoritative, but they do not reflect the real dependency graph for this project.

Two SBOMs live here (`sbom-github.json`, `sbom-npm.json`). Neither represents the actual components installed by npm. The npm lockfile (`package-lock.json`) is the closest thing to a source of truth for what ships.

Discrepancies (why these SBOMs are misleading):
- SBOMs lean on PURLs and generalized schemas; npm-specific resolution rules are lost.
- `package.json` intent (ranges, overrides, peer/optional deps, engines) is not captured by current SBOM specs.
- The resolved tree, integrity metadata, and platform-conditional behavior are in the lockfile, not the SBOMs.

If you want to know what this project actually uses, read the lockfile. The SBOMs are the nice-looking brochure, not the receipt.
