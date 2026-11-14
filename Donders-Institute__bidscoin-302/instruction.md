# Issue

**Title:** Quote SynthStrip image paths in local skullstrip commands

## Description

## Summary
Local skull stripping now quotes the source, output, and mask paths passed to `mri_synthstrip`. A dataset root containing spaces previously split these arguments; an apostrophe could produce malformed shell syntax. Quoting keeps shell metacharacters in these generated paths literal.

This is one command-line change plus a standard-library import. User-supplied extra options and the cluster submission path retain their existing behavior.

## Validation
One focused mocked check exercises the actual `skullstrip()` function with an empty synthetic image placeholder under a dataset root containing spaces, a semicolon, and an apostrophe. It captures the command, verifies all three paths and an extra option after shell tokenization, and returns a nonzero command status to skip downstream image processing.

The original command failed with an unmatched quotation; the patched command passes. The function and regression test were loaded from source in isolation to avoid unrelated package dependencies. The regression is included in `tests/test_skullstrip.py`.

No FreeSurfer process, real imaging data, network, tox matrix, or broad suite was run; local validation was limited to this focused check.

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `e4778a2bd09e`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.