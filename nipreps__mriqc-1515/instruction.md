# Issue

**Title:** ENH: Reserve only the processes the slowest DWI nodes use

## Description

The slowest DWI nodes each reserved every `--nprocs` process, so the runs of a dataset went through them one at a time:
- `hmc_b0` and `motion_correct` (3dvolreg) take one thread: AFNI's 3dvolreg isn't built with OpenMP, and 1 or 8 threads take the same time and give identical outputs.
- `dwimodel` (dipy's DKI fit) is single-threaded.
- `denoise` (svht_denoise) runs on `--omp-nthreads`.

Changes:
- **Reservations:** 3dvolreg and the DKI fit reserve one process; `denoise` reserves `--omp-nthreads`.
- **Memory:** the fit and the denoising now get a `mem_gb` scaled by the series size (measured peaks: 1.18 GB and 0.64 GB for a 0.12 GB series), since the reservation no longer keeps several of them apart.
- **Clean-up:** `pe.Node` ignores `num_threads=` (only `n_procs` counts), so those keywords are dropped. Every node keeps its reservation: I built the workflows on master and on this branch and compared them.

Two HBN runs (104×104×72×129), cold work directories, the 26.1.1 image with this PR's file mounted over it, `--nprocs 8 --omp-nthreads 4`, measured with `.maint/profile_nodes.py` (#1513):

| | 26.1.1 | This PR |
|---|---|---|
| DWI wall time | 27.8 min | 13.0 min |
| Total node time | 29.2 min | 29.3 min |
| Nodes running at once, on average | 1.0 | 2.3 |
| DWI IQMs | | all 53 identical, in both runs |

Tests:

```
python -m pytest mriqc/workflows/tests mriqc/tests/test_config.py
uvx ruff@0.17.3 check && uvx ruff@0.17.3 format --diff
```

GitHub's DWI job (`dfcheck --strict`) confirms the IQMs don't change.

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `587123f34093`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.