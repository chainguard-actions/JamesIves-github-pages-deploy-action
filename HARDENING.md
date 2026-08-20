<!-- markdownlint-disable -->

# Hardening Report: JamesIves--github-pages-deploy-action/v4.8.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **JamesIves--github-pages-deploy-action/v4.8.0** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): Direct expression interpolation inside `run:` blocks. In build.yml, `${{steps.unmodified.outputs.deployment-status}}` and `${{steps.modified.outputs.deployment-status}}` are interpolated directly into shell commands in two separate `run:` blocks. In production.yml, `${{ github.sha }}` is interpolated directly into a `git commit -m` shell command. In version.yml, `${{ secrets.GITHUB_TOKEN }}` is interpolated directly into an `echo` command that writes to `~/.npmrc`. Any `${{ ... }}` expression inside a `run:` block is a script-injection risk because YAML template substitution occurs before the shell ever sees the value.

Locations:

- `.github/workflows/build.yml:97`
- `.github/workflows/build.yml:113`
- `.github/workflows/production.yml:28`
- `.github/workflows/version.yml:62`

### unpinned-uses (severity: high)

All `uses:` references across every workflow file use mutable tag or version refs instead of immutable 40-character SHA digests, making them vulnerable to supply-chain attacks. Failing references include: build.yml — `actions/checkout@v6.0.1`, `actions/setup-node@v6.1.0`, `codecov/codecov-action@v5.5.2`, `actions/upload-artifact@v6.0.0`, `actions/download-artifact@v7.0.0`; deploy.yml — `actions/checkout@v6.0.1`, `JamesIves/github-pages-deploy-action@v4`; integration.yml — `actions/checkout@v6.0.1`, `JamesIves/github-pages-deploy-action@v4`, `dawidd6/action-delete-branch@v3.1.0`, `webfactory/ssh-agent@v0.9.1`, `actions/setup-node@v6.1.0`; label.yml — `actions/checkout@v6.0.1`, `mauroalderete/action-assign-labels@v1.5.1`; production.yml — `actions/checkout@v6.0.1`, `actions/setup-node@v6.1.0`; sponsors.yml — `actions/checkout@v6.0.1`, `JamesIves/github-sponsors-readme-action@v1`, `JamesIves/github-pages-deploy-action@v4`; version.yml — `nowactions/update-majorver@v1.1.2`, `actions/checkout@v6.0.1`, `actions/setup-node@v6.1.0`.

Locations:

- `.github/workflows/build.yml:17`
- `.github/workflows/deploy.yml:12`
- `.github/workflows/integration.yml:14`
- `.github/workflows/label.yml:14`
- `.github/workflows/production.yml:13`
- `.github/workflows/sponsors.yml:11`
- `.github/workflows/version.yml:12`

### missing-permissions (severity: medium)

Six workflow files have no top-level `permissions:` key and no job-level `permissions:` blocks on any of their jobs. Without explicit permissions, workflows run with the default (often broad) token permissions. Only label.yml defines explicit permissions. The affected files are: build.yml, deploy.yml, integration.yml, production.yml, sponsors.yml, and version.yml.

Locations:

- `.github/workflows/build.yml:1`
- `.github/workflows/deploy.yml:1`
- `.github/workflows/integration.yml:1`
- `.github/workflows/production.yml:1`
- `.github/workflows/sponsors.yml:1`
- `.github/workflows/version.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three finding types across 7 workflow files:

**script-injection** (4 locations):
- build.yml lines 97 & 113: Moved `${{steps.unmodified.outputs.deployment-status}}` and `${{steps.modified.outputs.deployment-status}}` into `env:` blocks as `DEPLOYMENT_STATUS`, referenced as `$DEPLOYMENT_STATUS` in run blocks.
- production.yml line 28: Moved `${{ github.sha }}`, `${{ secrets.GIT_CONFIG_EMAIL }}`, and `${{ secrets.GIT_CONFIG_NAME }}` into `env:` block, referenced as plain env vars in run block.
- version.yml line 62: Moved `${{ secrets.GITHUB_TOKEN }}` into `env:` block as `GITHUB_TOKEN_VALUE`, referenced as `$GITHUB_TOKEN_VALUE` in run block.

**unpinned-uses** (all 7 workflow files): Pinned all action references to full 40-char SHA digests with original tag in comment:
- actions/checkout@v6.0.1 → @8e8c483db84b4bee98b60c0593521ed34d9990e8
- actions/setup-node@v6.1.0 → @395ad3262231945c25e8478fd5baf05154b1d79f
- codecov/codecov-action@v5.5.2 → @671740ac38dd9b0130fbe1cec585b89eea48d3de
- actions/upload-artifact@v6.0.0 → @b7c566a772e6b6bfb58ed0dc250532a479d7789f
- actions/download-artifact@v7.0.0 → @37930b1c2abaa49bbe596cd826c3c89aef350131
- JamesIves/github-pages-deploy-action@v4 → @fa24774553152dd7873cd16ebd8d959b010c5445
- dawidd6/action-delete-branch@v3.1.0 → @d1efac9a6f7a9b408d4e8ff663a99c1fbac17b3f
- webfactory/ssh-agent@v0.9.1 → @a6f90b1f127823b31d4d4a8d96047790581349bd
- mauroalderete/action-assign-labels@v1.5.1 → @671a4ca2da0f900464c58b8b5540a1e07133e915
- JamesIves/github-sponsors-readme-action@v1 → @02650b8cd445fc16dfef73195f9c406dce041623
- nowactions/update-majorver@v1.1.2 → @f2014bbbba95b635e990ce512c5653bd0f4753fb

**missing-permissions** (6 workflow files): Added top-level `permissions:` blocks:
- build.yml: `contents: read`
- deploy.yml: `contents: write` (pushes to release branch)
- integration.yml: `contents: write` (deploys to gh-pages)
- production.yml: `contents: write` (commits and pushes)
- sponsors.yml: `contents: write` (deploys to dev branch)
- version.yml: `contents: write` (pushes tags and publishes)

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed three unquoted shell variable expansions: (1) build.yml ~line 115: `$DEPLOYMENT_STATUS` → `"$DEPLOYMENT_STATUS"` in the first [[ ]] condition checking for 'skipped' or 'success'; (2) build.yml ~line 141: `$DEPLOYMENT_STATUS` → `"$DEPLOYMENT_STATUS"` in the second [[ ]] condition checking for 'success'; (3) version.yml ~line 54: `npm version $VERSION` → `npm version "$VERSION"` to prevent word splitting/glob expansion of the tag-derived version string.

