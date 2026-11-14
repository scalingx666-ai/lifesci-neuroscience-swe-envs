# Issue

**Title:** Create website for CMU ECE CS Guide

## Description

# Summary of Changes

- Please indicate the classes you are modifying:
  - Adding a new website feature

# Checklist:

- [ x] I have added my entries to the `README.md` table of contents in the root directory as links.

Make sure that your contributions:

- [ x] Are accurate and will not mislead the reader
- [ x] Do not encourage or cause academic integrity violations
- [ x] Do not offend professors or unfairly criticize the university

## Details

Files Created

  .gitignore                          # Excludes node_modules, build output
  package.json                        # NPM config with VitePress
  .vitepress/
    config.mts                        # Main site configuration
    theme/
      index.ts                        # Custom theme entry
      style.css                       # Minimal styling with CMU red accent
  .github/workflows/
    deploy.yml                        # GitHub Actions for auto-deployment
  index.md                            # Home page with hero and feature cards
  cs_core/index.md                    # CS Core landing page
  ece_core/index.md                   # ECE Core landing page
  math_science_reqs/index.md          # Math/Science landing page
  electives/index.md                  # Electives landing page
  contributing.md                     # Contribution guidelines

  Features

  - Search: Built-in local search indexes all 102 course pages
  - Navigation: Sidebar organized by category (electives grouped by topic: ML, Systems, Robotics, etc.)
  - Clean URLs: /cs_core/15122 instead of /cs_core/15122.html
  - Edit links: "Edit this page on GitHub" on every page
  - Auto-deploy: Pushes to master automatically deploy to GitHub Pages

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `6c0c9db341dc`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.