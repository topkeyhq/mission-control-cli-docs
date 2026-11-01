# Mission Control CLI

Control Mission Control projects and bots from a terminal or automation. The CLI uses the canonical League Cloud API; server permissions apply to every operation. It does not run a second workflow engine.

Requires Node.js 22 or newer. Source is proprietary. Public documentation does not grant source redistribution rights.

## Install and sign in

Requires Node.js 22+ and GitHub CLI access to this private repository:

```sh
release_dir=$(mktemp -d)
gh release download --repo topkeyhq/just-ship-league-mission-control-cli --pattern '*.tgz' --pattern SHA256SUMS --dir "$release_dir"
(cd "$release_dir" && shasum -a 256 -c SHA256SUMS)
npm install --global --ignore-scripts "$release_dir"/*.tgz
mc login
mc whoami
```

`mc login` opens independent Google browser sign-in; no running Mac app or pairing code is required. Observation is the default scope. Use `mc login --browser --scope operate` for task/session actions, or `--scope full` for administration within your existing roles. The default API is production League Cloud. Each login creates an independently revocable CLI device; no Keychain prompts. Use `mc logout` to revoke it.

```sh
mc factories list
mc config set --factory FACTORY_UUID --project owner/repository
mc bots list
mc runs list --json
mc update
```

For automation, use `mc auth login --pair-stdin` or `--token-stdin`, or inject `MC_TOKEN` from your secret store. Set `MC_ENDPOINT` for another authorized API origin. Never put credentials in command arguments or logs.

`mc update` downloads the latest stable GitHub Release, verifies its checksum, and installs it. With mise, install from the directory whose Node version you use, and run `mise reshim` if needed. Source/releases are private; [documentation is public](https://github.com/topkeyhq/mission-control-cli-docs). See [Bellman release procedure](docs/releases.md).

[Authentication](docs/authentication.md) · [Command reference](docs/commands.md) · [Workflows](docs/workflows.md) · [Automation](docs/automation.md) · [Troubleshooting and compatibility](docs/compatibility.md)

[Independent sign-in](docs/independent-signin.md) · [Independent workers](docs/workers.md) · [Cross-client migration coverage](docs/migration-parity.md)
