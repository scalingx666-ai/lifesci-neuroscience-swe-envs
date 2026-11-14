# Issue

**Title:** ENH: Load the BOLD series into memory before head-motion resampling

## Description

`apply_hmc` resampled the BOLD series with nitransforms, reading it from the gzipped file. nitransforms reads one volume at a time, and each read decompresses the file again up to that volume, so the time grows with the square of the series length.

- **Change:** the series is loaded once and handed to nitransforms in memory.
- **Same output, bit for bit,** and BOLD runs take about half the time:

  | ds002785 `sub-0002`, 480 volumes (80×80×36) | 26.1.1 | This PR |
  |---|---|---|
  | `apply_hmc` | 538 s | 53 s |
  | BOLD workflow, wall time | 17.2 min | 9.2 min |
  | BOLD IQMs | | all 44 identical |

  Both from cold work directories, with the 26.1.1 image (this PR's file mounted over it), `--nprocs 8 --omp-nthreads 4`, measured with `.maint/profile_nodes.py` (#1513). The remaining 410 s are 3dvolreg's motion estimation.
- **Memory:** the series is also held in memory, 0.41 GB for this run. The node's peak (12 GB here) is still nitransforms computing the coordinates of every volume at once, so `apply_hmc` keeps reserving every CPU.

Tests:

```
python -m pytest mriqc/workflows/tests/test_hmc.py
uvx ruff@0.17.3 check && uvx ruff@0.17.3 format --diff
```

`test_apply_transforms_in_memory` checks that the output equals what nitransforms produces reading the file. CircleCI's BOLD check (`dfcheck --strict`) confirms the IQMs don't change.

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `587123f34093`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.