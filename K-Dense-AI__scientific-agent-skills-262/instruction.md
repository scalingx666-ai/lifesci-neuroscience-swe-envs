# Issue

**Title:** Add companion identifier APIs to ontology-term-resolution

## Description

## Summary
- Extend `ontology-term-resolution` (v1.0 → v1.1) with Bioregistry, Identifiers.org, ZOOMA, and Ontobee helpers. OLS remains the only authority for emitting or validating a term.
- `lookup_prefix.py` answers prefix/CURIE *shape* questions (`HPO` vs `HP`, local-id pattern, landing pages). `map_terms.py` maps lab shorthand after OLS lexical search misses; `--ontology` is required because unfiltered ZOOMA returns FOODON/XAO/BTO at HIGH for `liver`.
- Documented live-API traps in `references/companion-apis.md`, including Identifiers.org rejecting synonym prefixes and `%3A`-encoded colons.

@TKassis — requesting your review.

## Test plan
- [x] `uv run skills-ref validate skills/ontology-term-resolution`
- [x] `uv run --with pytest python -m pytest tests/ontology-term-resolution -q` (75 passed, 6 live OLS tests skipped)
- [x] `uv run --with pytest python -m pytest tests/_meta -q`
- [x] Live `lookup_prefix.py HP HPO HP:0001250 HPO:0001250 HP:notanid` — synonym rewrite, invalid local id, Identifiers.org colon kept unencoded
- [x] Live `map_terms.py PBMC --ontology cl --exact-only` → `CL:2000001` HIGH; `validate_terms.py CL:2000001` → `ok` (peripheral blood mononuclear cell)
- [ ] Reviewer: skim `SKILL.md` “Which service” table and confirm OLS-still-decides is the contract you want

Made with [Cursor](https://cursor.com)

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `515742b7ed52`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.