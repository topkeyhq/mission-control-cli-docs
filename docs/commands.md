# Command reference

Run `mc --help` for the complete discoverable command list. `mission-control` is an alias for `mc`.

`mc login`, `mc whoami`, and `mc logout` are convenient authentication commands. `mc update` installs the latest checksummed stable release. Maintainers use `mc release --dry-run` and `mc release --bump patch|minor|major|auto` in a clean main checkout.

Global options: `--endpoint HTTPS_ORIGIN`, `--factory UUID`, `--project owner/repo`, `--id UUID`, `--request-id UUID`, `--file payload.json` (or `-` for stdin), and `--json`. Store scope with `mc config set --factory UUID --project owner/repo`; inspect it with `mc config show`. Arguments override stored defaults. `MC_ENDPOINT` overrides the stored endpoint.

| Commands | Scope and purpose |
|---|---|
| `status`, `capabilities` | Remote status and capability discovery |
| `factories list`, `factories show` | Accessible factories and project configuration |
| `bots list`, `bots colors`, `ledger list` | Factory bot roster, synchronized profile colors and activity |
| `bots color --bot ID --color '#RRGGBB'` | Set one profile's shared card/phase accent; use an exact record key from `bots colors` when IDs are ambiguous |
| `execution show`, `execution set`, `execution verify` | Runtime connection configuration and verification |
| `records list`, `records set` | Revision-bound synchronized records |
| `runtime show`, `runtime set` | Project bot runtime |
| `schedules list`, `schedules set`, `schedules delete`, `schedules conflicts` | Project schedules and provider conflicts |
| `events list --since CURSOR --checkpoint HASH`, `metrics show` | Restore-safe receipt history and shared receipt counts |
| `worker register`, `worker run`, `worker recover` | Host-local registration, execution and saved-result reconciliation; see [workers](workers.md) |
| `workers list`, `workers revoke --id UUID` | Project worker roster and revocation |
| `runs list`, `runs inspect --id UUID` | Execution history and bounded inspection |
| `tasks list`, `tasks submit` | Native project tasks |
| `handoffs submit`, `handoffs show --id UUID` | One-time bot runs or configured pipeline kickoffs, with durable receipts |
| `sessions message`, `sessions cancel`, `sessions archive`, `sessions unarchive` | Server-owned run controls (`--id RUN_UUID`); use `--request-id` for a stable operation UUID |
| `operations show --id UUID` | Read your session operation receipt |
| `onboarding`, `environments list`, `org show`, `members list`, `invites list`, `devices list` | Accessible onboarding and organization configuration |
| `projects set`, `projects delete`, `rules set`, `factories create`, `runtime delete` | Administrative configuration using canonical payloads |
| `openapi` | Download the published API schema without authentication |
| `health` | Project readiness |
| `automation set`, `mode set` | Project automatic routing and watch/live mode |
| `requests list`, `requests replay --id UUID` | Local durable submissions and exact replay |
| `api METHOD /v1/path` | Authenticated escape hatch for canonical API operations |

Mutation payloads are read from `--file`. They follow the server's versioned API schema; the CLI does not silently invent missing configuration, overwrite revisions, or retry mutation requests. `execution verify` sends an empty POST without a file. `schedules delete` supplies the schedule id in its JSON body. `records set` must retain the revision observed in the corresponding read.

`--json` emits one object on stdout: `{"schemaVersion":1,"ok":true,"data":...}`. API responses remain under `data`; durable submissions wrap the receipt with `requestID` and `data`. Errors go to stderr as `{"schemaVersion":1,"ok":false,"error":{"code":4,"message":"API returned HTTP 403.","status":403}}`. Human output is indented JSON except the formatted Home briefing. Handoff/task persistence notices go only to stderr. Scripts must check the process exit status.

Exit codes: 0 success; 2 usage/local input or other HTTP rejection; 3 unauthenticated; 4 forbidden; 5 not found; 6 conflict; 7 network/timeout with potentially unknown acceptance; 8 rate limited; 9 upstream/server/invalid JSON response. Error bodies are not echoed because they may contain secrets.

Completion: `mc completion bash > ~/.mission-control-completion.bash`, then source that file from `.bashrc`. For zsh, save `mc completion zsh` as `_mc` in a directory on `fpath`, then run `compinit`. Completion currently covers top-level commands.

Resume receipt history with both returned values: `events list --since CURSOR --checkpoint HASH`.
A 409 means the acknowledged history changed (for example after restore); rebuild
from `--since 0`. Preserve local drafts and never replay mutation requests merely
to rebuild observation history. Numeric-only cursors remain compatible but cannot
detect sequence reuse after a database restore.

## Shared workflow commands

`workflows show`; `proof show|assess`; `reviews show|prepare|finding|resolve|complete|dispatch`; `learning show|seal|observe|promote|forget`; `guidance history|set|restore`; `checkpoints list|create|prepare-resume|release|dispatch|export`; `evaluations list|submit|cancel|worker`; `requests show`. See [payloads, flags and recovery](shared-workflows.md). Notebook edits require `--notebook-version`; proof reads use `--pr`.

## Recorded workflow evidence

`mc cards timeline --pr NUMBER --factory UUID --project owner/repo --json` returns the canonical recorded selection and its stages without adding project-roster bots. `workflow.selection: "unknown"` and empty `stages` mean no selected pipeline is recorded, not that every bot finished. One-time runs remain under `runs`; stale revisions do not advance current stages. Merge/release state is separate from execution completion. Human output preserves the same fields.

## Home and Insights

`mc home` (or bare `mc`) reads the latest 100 accessible execution receipts for the configured factory/project. Pass `--factory` and `--project`, or save them with `mc config set`. Blocked, failed, rejected, exhausted, and uncertain receipts come first; reported-complete receipts suggest inspection. The briefing never launches, retries, or changes anything. Its suggested inspection commands use the current scope. `mc --help` still opens help.

`mc home --json` retains the standard envelope and returns `data.version: 1`, `repository`, `observedAt`, `items`, `reportedCompleteToday`, `observedReceipts`, and `coverage`. Items include `id`, `bot`, `state`, `createdAt`, `kind` (`attention` or `review`), and `action`. Today groups by receipt creation date in the local timezone. A complete receipt is not independent task-success evidence; sent and accepted never count as complete. Unknown states are counted as observed but get no invented action. API failures use existing exit codes, without a fabricated empty-success summary.

`mc insights` reads the same metrics as `mc metrics show`; both remain supported. Configure with `mc config`, inspect occasional history with `mc insights`, and use Home for next steps.

Session message receipts can report `queued`: Conductor accepted the message but is waiting to deliver it, including during workspace/agent startup. Both normal and `--json` output preserve this state. It is not task completion. Use `runs inspect` for current activity and `operations show --id <requestID>` for the original submission receipt; replay never sends a queued message again. Existing CLI versions display this additive state without an upgrade.

## Personal merge queues

Each signed-in engineer has their own queue for each repository. The same account sees the same order on Mac, Web, iPhone and CLI. Select a factory and project, then run:

```sh
mc queue add --prs 481,482,483
mc queue status
mc queue land --bot BOT
```

Use `mc queue status --json` for machine-readable observations. On the landing command, `--owner LOGIN` selects the GitHub merge identity. Your signed-in Mission Control account still owns the queue. GitHub confirms each merge before the coordinator advances.
