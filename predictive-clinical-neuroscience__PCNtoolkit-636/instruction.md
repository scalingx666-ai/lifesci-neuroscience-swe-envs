# Issue

**Title:** enh: add cross-platform smoke-test CI

## Description

#### AI assistance

Claude Opus 5.5 (Claude Code) planned and wrote the workflow and the smoke
script, ran them locally and on my fork, and drafted this description. I
reviewed the changes.

#### Reference issue (if any)

#### What does this implement/fix?

## Summary

A new `smoke-test` workflow. It installs the package on Linux, macOS and
Windows, and checks that the basic workflow runs. The full test suite stays
in `testing.yml` and still runs on Linux only.

```diff
 .github/
+├── scripts/
+│   └── smoke_test.py      # BLR + HBR on 200 rows of synthetic data
 └── workflows/
+    └── smoke-test.yml     # {ubuntu, macos, windows} x {3.12, 3.13}
```

Each of the 6 jobs:

```text
pip install .                  # not editable: tests what users install
cd $RUNNER_TEMP                # outside the checkout, so the installed copy is imported
python -c "import pcntoolkit"
normative --help
python smoke_test.py
  BLR  fit_predict                      -> Z finite, shape == Y
  BLR  save -> NormativeModel.load -> predict -> Z finite, shape == Y
  HBR  fit_predict (20 tune, 20 draws, 1 chain, 1 core)  -> Z finite, shape == Y
```

The HBR run is very short. It only checks that sampling works on each
platform, not that the model fits well.

Triggers: same as `testing.yml` (push to `dev`, PR to `dev`), plus
`workflow_dispatch`. A 30-minute timeout stops a stuck sampler.

## Evidence

- **Before:** no CI on macOS or Windows.
  **After:** all 6 jobs pass in 2–3.5 min on my fork:
  https://github.com/gdevenyi/PCNtoolkit/actions/runs/42641615608

Windows, Python 3.12 (pip only, no conda, no g++):

```text
[smoke] pcntoolkit 1.4.0 from C:\hostedtoolcache\windows\Python\3.12.10\x64\Lib\site-packages\pcntoolkit\__init__.py
[smoke] BLR fit_predict: OK, Z shape (200, 2)
[smoke] BLR load and predict: OK, Z shape (200, 2)
[smoke] HBR fit_predict: OK, Z shape (200, 2)
[smoke] all checks passed
```

This also answers the open question on the
[Windows wiki page](https://github.com/predictive-clinical-neuroscience/PCNtoolkit/wiki/PCNtoolkit-from-Windows):
with pytensor 3, HBR runs on Windows without the `m2w64-toolchain` compiler.

## Merge Danger

**Door:** two-way

Only new files are added. To roll back, delete the two files.

**Blast Radius:** CI-only

No package code changes. Each push or PR to `dev` adds 6 short jobs.
macOS runners use more of the Actions minutes quota than Linux runners.

#### To reproduce

```bash
pip install .
cd "$(mktemp -d)"
python /path/to/PCNtoolkit/.github/scripts/smoke_test.py
```

🤖 Generated with [Claude Code](https://claude.com/claude-code)

https://claude.ai/code/session_01W53iURfHXS7T92s6tketPc

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `a096d2496af5`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.