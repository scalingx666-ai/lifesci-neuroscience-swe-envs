# Issue

**Title:** Restore detection of proxy and PDF-viewer wrapped paper URLs

## Description

Stacked on #355 (base `fix-various-issues`). Reverses the paper-detection regression #355 called out ("URLs wrapped by PDF-viewer extensions or EZproxy are no longer recognized by the popup") while keeping host anchoring.

### What changes
- New `unwrapUrl(url)` in `sources/index.js`. If no source matches `url`, it returns the first `http(s)://` URL embedded in the path or in a query value that a source does match. Percent-encoded URLs work. The fragment is never searched, and only one level is unwrapped. Otherwise it returns `url` unchanged.
- Applied at the entry points: `isPaper`, `parseIdFromUrl`, `makePaper`, `addOrUpdatePaper`. Sources only ever see the paper URL, and `urlHashToId` stores the inner URL, never the proxy's. Website parsing (`tab` passed) and the content script's DOM checks such as `isArxivAbstractUrl` keep the raw URL.
- Wrapped URLs auto-add from the content script, as before #355.

### Detection, before → after
| URL | #346 | #355 | this PR |
|---|---|---|---|
| `chrome-extension://…/https://arxiv.org/pdf/…` | ✅ | ❌ | ✅ |
| `…/viewer.html?file=https%3A%2F%2Farxiv.org…` | ❌ | ❌ | ✅ |
| `…/login?url=https://pubs.acs.org/doi/…` | ✅ (but `urlToId` threw) | ❌ | ✅ |
| `proxy.univ.edu/https://www.nature.com/…` | ✅ | ❌ | ✅ |
| `pubs-acs-org.ezproxy.univ.edu/…` (host rewrite) | ❌ | ❌ | ❌ (out of scope) |
| `evil.example/?x=arxiv.org/abs/…` (no scheme), `#https://…` (in the fragment), `arxiv.org.evil.example` (host suffix), `notarxiv.org` | ✅/❌ | ❌ | ❌ |
| `evil.example/?x=https://arxiv.org/abs/…` | ✅ | ❌ | ✅ (accepted: can't be told apart from `login?url=`) |

`http://` ChemRxiv and OUP stay detected, as #355 introduced.

### Testing
- utils + meta: 290 passing, 6 pending. New: a positive wrapped test per source (4 wrapper shapes), a `unwrapUrl` table, and `parseIdFromUrl` on wrapped and encoded URLs. The #355 cross-host negatives are unchanged.
- `npm run build` (chrome + firefox) OK; prettier clean.
- A Codex review found 3 unwrapping bugs, all fixed with tests: encoded paths that dropped the inner query, a raw candidate chosen over its decoded form, and a scan that reached into the authority.

### Known edges
- A website record saved under a wrapper URL now resolves to the paper instead. Accepted, since #355 never shipped.
- Raw inner paths that carry their own encoded delimiters (e.g. `10.1007%2F…`) get decoded. Prefix matching is unaffected.

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `f786584f4738`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.