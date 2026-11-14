# Issue

**Title:** Sparse conn

## Description

speedup calc_surface_connectivity using sparse matrix generation (instead of manually adding each face)
around 10x faster when tested on fsLR-32k

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `3b9469edf028`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.