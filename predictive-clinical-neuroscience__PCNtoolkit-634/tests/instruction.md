# Issue

**Title:** Fix links in the docs

## Description

I ran `sphinx-build -b linkcheck doc/ doc/_build/linkcheck` and fixed the issues that it showed me.

We had no broken link, just some redirects that are fixed now to point to the correct link.

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `2bb7e6751e2f`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.