<!-- markdownlint-disable -->

# Hardening Report: JamesIves--github-pages-deploy-action/3.6.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **JamesIves--github-pages-deploy-action/3.6.2** was hardened automatically. 11 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ ... }} expression is directly interpolated inside a run: shell command string. In publish.yml, the step 'Authenticate with the GitHub Package Registry' uses `echo "//npm.pkg.github.com:_authToken=${{ secrets.GITHUB_TOKEN }}" > ~/.npmrc` — the expression is expanded by the YAML template engine before the shell sees it, enabling injection if the value contains shell metacharacters.

Locations:

- `.github/workflows/publish.yml:44`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks. Unpinned references: actions/checkout@v1, actions/setup-node@v1, codecov/codecov-action@v1.

Locations:

- `.github/workflows/build.yml:15`
- `.github/workflows/build.yml:18`
- `.github/workflows/build.yml:27`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags instead of immutable 40-character commit SHAs. Unpinned references: actions/checkout@v2, github/codeql-action/init@v1, github/codeql-action/autobuild@v1, github/codeql-action/analyze@v1.

Locations:

- `.github/workflows/codeql-analysis.yml:18`
- `.github/workflows/codeql-analysis.yml:29`
- `.github/workflows/codeql-analysis.yml:35`
- `.github/workflows/codeql-analysis.yml:40`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of immutable 40-character commit SHAs. Unpinned references: actions/checkout@v1, actions/checkout@v2, JamesIves/github-pages-deploy-action@releases/v3, dawidd6/action-delete-branch@v2.0.1, webfactory/ssh-agent@v0.2.0, actions/setup-node@v1.

Locations:

- `.github/workflows/integration.yml:14`
- `.github/workflows/integration.yml:18`
- `.github/workflows/integration.yml:29`
- `.github/workflows/integration.yml:33`
- `.github/workflows/integration.yml:44`
- `.github/workflows/integration.yml:55`
- `.github/workflows/integration.yml:59`
- `.github/workflows/integration.yml:70`
- `.github/workflows/integration.yml:74`
- `.github/workflows/integration.yml:85`
- `.github/workflows/integration.yml:91`
- `.github/workflows/integration.yml:102`
- `.github/workflows/integration.yml:106`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of immutable 40-character commit SHAs. Unpinned references: actions/checkout@v1, actions/checkout@v2, JamesIves/github-pages-deploy-action@releases/v3-test, dawidd6/action-delete-branch@v2.0.1, webfactory/ssh-agent@v0.2.0, actions/setup-node@v1.

Locations:

- `.github/workflows/integration-beta.yml:13`
- `.github/workflows/integration-beta.yml:17`
- `.github/workflows/integration-beta.yml:28`
- `.github/workflows/integration-beta.yml:32`
- `.github/workflows/integration-beta.yml:43`
- `.github/workflows/integration-beta.yml:54`
- `.github/workflows/integration-beta.yml:58`
- `.github/workflows/integration-beta.yml:69`
- `.github/workflows/integration-beta.yml:73`
- `.github/workflows/integration-beta.yml:84`
- `.github/workflows/integration-beta.yml:90`
- `.github/workflows/integration-beta.yml:101`
- `.github/workflows/integration-beta.yml:105`

### unpinned-uses (severity: high)

Workflow file references actions using mutable tags instead of immutable 40-character commit SHAs. Unpinned references: actions/checkout@v2, actions/setup-node@v1.4.2 (used twice).

Locations:

- `.github/workflows/publish.yml:9`
- `.github/workflows/publish.yml:14`
- `.github/workflows/publish.yml:38`

### missing-permissions (severity: medium)

Workflow file has no top-level permissions: key and no job-level permissions: keys on any job. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially broad) permissions. All permissions should be explicitly declared with least-privilege scopes.

Locations:

- `.github/workflows/build.yml:1`

### missing-permissions (severity: medium)

Workflow file has no top-level permissions: key and no job-level permissions: keys on any job. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially broad) permissions.

Locations:

- `.github/workflows/codeql-analysis.yml:1`

### missing-permissions (severity: medium)

Workflow file has no top-level permissions: key and no job-level permissions: keys on any job. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially broad) permissions.

Locations:

- `.github/workflows/integration.yml:1`

### missing-permissions (severity: medium)

Workflow file has no top-level permissions: key and no job-level permissions: keys on any job. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially broad) permissions.

Locations:

- `.github/workflows/integration-beta.yml:1`

### missing-permissions (severity: medium)

Workflow file has no top-level permissions: key and no job-level permissions: keys on any job. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially broad) permissions.

Locations:

- `.github/workflows/publish.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all 11 findings across 5 workflow files:

1. script-injection (publish.yml): Moved `${{ secrets.GITHUB_TOKEN }}` from the `run:` shell string into the step's `env:` block as `GITHUB_TOKEN`, referencing it as `${GITHUB_TOKEN}` in the shell script.

2. unpinned-uses: Pinned all action references to full 40-character commit SHAs:
   - actions/checkout@v1 → 50fbc622fc4ef5163becd7fab6573eac35f8462e
   - actions/checkout@v2 → 0717577d45739eb3c851188b29f50ed6c0b2194e
   - actions/setup-node@v1 → f1f314fca9dfce2769ece7d933488f076716723e
   - actions/setup-node@v1.4.2 → 44c9c187283081e4e88b54b0efad9e9d468165a4
   - codecov/codecov-action@v1 → 29386c70ef20e286228c72b668a06fd0e8399192
   - github/codeql-action/{init,autobuild,analyze}@v1 → 231aa2c8a89117b126725a0e11897209b7118144
   - JamesIves/github-pages-deploy-action@releases/v3 → 132898c54c57c7cc6b80eb3a89968de8fc283505
   - JamesIves/github-pages-deploy-action@releases/v3-test → 132898c54c57c7cc6b80eb3a89968de8fc283505 (branch no longer exists; used releases/v3 SHA)
   - dawidd6/action-delete-branch@v2.0.1 → dc1ec13ead3bfdde79d713b8d3bf92c12df7a2d9
   - webfactory/ssh-agent@v0.2.0 → b6c65becb03b5d9487d1ddb2ab9f28bdffa1fa77

3. missing-permissions: Added explicit permissions blocks to all 5 workflow files with least-privilege scopes appropriate to each workflow's function.

