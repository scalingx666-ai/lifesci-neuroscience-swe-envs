# Issue

**Title:** Fix return statement in MMP case for atlas selection

## Description

This pull request makes a small but important fix to the `_select_hemi` function in `pyvista_plotters.py`. The change corrects how the `atlas` variable is passed to `_select_hemi` when the `parc_str` is `'mmpall'`, ensuring the function receives the expected argument type.

* Bug fix:
  * [`netneurotools/plotting/pyvista_plotters.py`](diffhunk://#diff-fc777acd6a68141e34b84a7d95ca5fdafe5cc785be5a69b66b54d33bcc2555a6L896-R896): Updated the call to `_select_hemi` to pass the entire `atlas` object instead of just `atlas[key]`, which resolves a potential error in hemisphere selection for the `'mmpall'` parcellation.

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `de0bcf328a57`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.