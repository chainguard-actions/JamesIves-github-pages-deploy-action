<!-- markdownlint-disable -->

# Hardening Report: JamesIves--github-pages-deploy-action/v4.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **JamesIves--github-pages-deploy-action/v4.9.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files reference actions by mutable version tags instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if a tag is moved or hijacked.

build.yml: actions/checkout@v7.0.1, actions/setup-node@v7.0.0, codecov/codecov-action@v7.0.0, actions/upload-artifact@v7.0.1, actions/download-artifact@v8.0.1
deploy.yml: actions/checkout@v7.0.1, JamesIves/github-pages-deploy-action@v4
integration.yml: actions/checkout@v7.0.1, webfactory/ssh-agent@v0.10.0
label.yml: actions/checkout@v7.0.1, mauroalderete/action-assign-labels@v1.5.1
production.yml: actions/checkout@v7.0.1, actions/setup-node@v7.0.0
release.yml: actions/checkout@v7.0.1
sponsors.yml: actions/checkout@v7.0.1, JamesIves/github-sponsors-readme-action@v1, JamesIves/github-pages-deploy-action@v4
version.yml: nowactions/update-majorver@v1.1.2, actions/checkout@v7.0.1, actions/setup-node@v7.0.0

Locations:

- `.github/workflows/build.yml:21`
- `.github/workflows/deploy.yml:13`
- `.github/workflows/integration.yml:29`
- `.github/workflows/label.yml:17`
- `.github/workflows/production.yml:32`
- `.github/workflows/release.yml:29`
- `.github/workflows/sponsors.yml:11`
- `.github/workflows/version.yml:18`

### script-injection (severity: high)

Multiple run: blocks directly interpolate ${{ ... }} expressions into shell commands (sub-rule a), allowing expression values to be parsed as shell syntax before the shell ever sees them.

build.yml line 106: `${{steps.unmodified.outputs.deployment-status}} = skipped` — steps.*.outputs.* context interpolated directly in run: block.
build.yml line 130: `${{steps.modified.outputs.deployment-status}} = success` — same issue.

release.yml line 36: `git config user.email "${{ secrets.GIT_CONFIG_EMAIL }}"` — secrets.* interpolated directly in run: block.
release.yml line 44: `NEXT_VERSION=$(npm --no-git-tag-version version "${{ inputs.bump }}")` — inputs.* interpolated directly in run: block.
release.yml line 65: `git checkout -b ${{ steps.version.outputs.target-branch }}` — steps.*.outputs.* interpolated directly in run: block (unquoted).
release.yml line 71: `git fetch origin ${{ steps.version.outputs.target-branch }}` — same issue.
release.yml line 92: `git push origin ${{ steps.version.outputs.target-branch }}` — same issue.
release.yml line 118: `gh release create "v${{ needs.prepare.outputs.next-version }}"` — needs.*.outputs.* interpolated directly in run: block.

production.yml line 60: `git config user.email "${{ secrets.GIT_CONFIG_EMAIL }}"` — secrets.* interpolated directly in run: block.
production.yml line 63: `git commit -m "Deploy Production Code for Commit ${{ github.sha }} 🚀"` — github.* interpolated directly in run: block.

integration.yml: Multiple run: blocks interpolate ${{ github.repository }} and ${{ github.run_id }} directly into curl URLs, e.g. `https://api.github.com/repos/${{ github.repository }}/branches/integration-test-dry-run`.

version.yml line 59: `npm version $VERSION` — $VERSION is derived from GITHUB_REF (set from ${{ github.ref }}) and is used unquoted in the shell command (sub-rule b).
version.yml line 78: `echo "//npm.pkg.github.com:_authToken=${{ secrets.GITHUB_TOKEN }}" > ~/.npmrc` — secrets.* interpolated directly in run: block (sub-rule a).

Locations:

- `.github/workflows/build.yml:106`
- `.github/workflows/build.yml:130`
- `.github/workflows/release.yml:36`
- `.github/workflows/release.yml:44`
- `.github/workflows/release.yml:65`
- `.github/workflows/release.yml:71`
- `.github/workflows/release.yml:92`
- `.github/workflows/release.yml:118`
- `.github/workflows/production.yml:60`
- `.github/workflows/production.yml:63`
- `.github/workflows/integration.yml:310`
- `.github/workflows/integration.yml:340`
- `.github/workflows/version.yml:59`
- `.github/workflows/version.yml:78`

### github-env-injection (severity: high)

In release.yml, the 'Compute the next version and target release branch' step writes $NEXT_VERSION (derived from inputs.bump via `npm version "${{ inputs.bump }}"`) and $NEXT_MAJOR to $GITHUB_OUTPUT without applying the required sanitization step (`printf '%s' ... | tr -d '\n\r'`) before the write. Although npm processes the value, $NEXT_VERSION is still transitively derived from the untrusted inputs.bump context and is written unsanitized:
  echo "next-version=$NEXT_VERSION" >> "$GITHUB_OUTPUT"
  echo "target-branch=releases/v$NEXT_MAJOR" >> "$GITHUB_OUTPUT"

Locations:

- `.github/workflows/release.yml:54`
- `.github/workflows/release.yml:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection

**Notes:**

Fixed all three findings across 8 workflow files:

1. unpinned-uses: Pinned all 10 distinct action references to full 40-char SHAs with original tags as comments. Verified no @v-style references remain.

2. script-injection: Moved all ${{ }} expressions out of run: shell commands into env: blocks. Key fixes: build.yml (steps outputs), release.yml (secrets, inputs.bump, steps outputs, needs outputs), production.yml (secrets, github.sha), integration.yml (github.repository and github.run_id in curl URLs), version.yml (secrets.GITHUB_TOKEN in echo, quoted $VERSION in npm version).

3. github-env-injection: In release.yml's 'Compute the next version' step, sanitized NEXT_VERSION and NEXT_MAJOR before writing to $GITHUB_OUTPUT using printf '%s' | tr -d '\n\r'.

