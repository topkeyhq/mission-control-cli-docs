# Mission Control CLI

Control Mission Control projects and bots from a terminal or automation. The CLI uses the canonical League Cloud API; server permissions apply to every operation. It does not run a second workflow engine.

Requires Node.js 22 or newer. Source is proprietary. Public documentation does not grant source redistribution rights.

## Install

Download the versioned `.tgz` asset from the CLI release and install it:

```sh
npm install --global ./topkeyhq-mission-control-cli-0.1.0.tgz
mc --version
mc --help
```

Download archives from the [private source releases](https://github.com/topkeyhq/just-ship-league-mission-control-cli/releases); organization access is required. Release publishing is coordinated with the cross-surface integration release. Maintainers can also build an archive with `npm pack`. This package is not currently published to the public npm registry.

Public documentation: [Mission Control CLI guide](https://github.com/topkeyhq/mission-control-cli-docs).

## First connection

Use the API origin supplied by your administrator, not the web gateway URL. Generate a pairing code in your existing authorized Mission Control session. Pipe it without including it in command arguments or shell history:

```sh
mc config set --endpoint https://YOUR_API_ORIGIN
# secret-manager read represents your organization's secret retrieval command.
secret-manager read mission-control-pairing-code | mc auth login --pair-stdin
mc auth status
mc factories list
mc config set --factory FACTORY_UUID --project owner/repository
mc bots list
mc runs list --json
```

An existing device token can be supplied through `auth login --token-stdin`. CI should inject `MC_TOKEN` and `MC_ENDPOINT` from its secret store. Never commit credentials or echo them in job logs.

[Authentication](docs/authentication.md) · [Command reference](docs/commands.md) · [Workflows](docs/workflows.md) · [Automation](docs/automation.md) · [Troubleshooting and compatibility](docs/compatibility.md)
