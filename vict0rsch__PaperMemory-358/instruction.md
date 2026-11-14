# Issue

**Title:** Release 1.2.0

## Description

Prepares the 1.2.0 release (first release since 1.0.3; 1.1.x was never published).

- Bump version to 1.2.0 in `package.json`, `package-lock.json` and the Readme badges (the manifest picks it up via `wxt.config.js`).
- Add a 1.2.0 entry to the popup's What's new modal covering #334, #341, #346, #355 and #356.
- Docs: update the "From source" install steps for the WXT build (`dist/chrome-mv3/`, `dist/firefox-mv2/`), refresh the acknowledgements (WXT/Vite, cloakbrowser), and add the missing sources (AIP, Cell, ChemRxiv, IHEP, JMLR) to the supported venues lists.

Verified: `npm run build:chrome` passes and the generated manifest reports `1.2.0`.

After merging: tag `1.2.0` so the Readme release badge and the What's new link resolve.

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `d6bbea7375cf`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.