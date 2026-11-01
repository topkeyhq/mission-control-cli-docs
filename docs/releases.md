# Bellman release procedure

Mission Control CLI follows the Topkey CLI release pattern: Bellman prepares a version PR; merging it runs GitHub Actions validation, tagging and GitHub Release publication. Source and install assets remain private; documentation is public. No public npm registry is required.

In a clean, up-to-date main checkout, run `mc release --dry-run` to review the inferred Conventional Commit bump, then `mc release` to prepare the release PR. `--bump patch|minor|major` overrides the inference. The command refuses another repository, a dirty checkout, a stale main, or no changes since the current version tag.

Bellman must review test results and the version change, use the configured project permissions for merging, then wait for the Release CLI workflow to succeed. Verify the release includes its versioned archive and SHA256SUMS. Report the actual published release URL. Never report a draft as published, bypass a failed check, or rewrite an existing version. Retrying a release must preserve the exact source commit.

To install the latest stable version, with Node 22+ and GitHub CLI authenticated for this private repository:

```sh
release_dir=$(mktemp -d)
gh release download --repo topkeyhq/just-ship-league-mission-control-cli --pattern '*.tgz' --pattern SHA256SUMS --dir "$release_dir"
(cd "$release_dir" && shasum -a 256 -c SHA256SUMS)
npm install --global --ignore-scripts "$release_dir"/*.tgz
mc login
mc whoami
```

Run `mc update` for future releases. It downloads the latest stable release using `gh`, verifies the SHA-256 checksum, and installs it without lifecycle scripts. GitHub access and Mission Control login are separate identities: GitHub authorizes installation; your own paired Mission Control device authorizes API operations.

A configured Bellman runtime and release automation must also be enabled for this repository in Mission Control. The repository workflow alone does not provision or authorize a bot.
