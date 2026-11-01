# Command reference

Run `mc --help` for the complete discoverable command list. `mission-control` is an alias for `mc`.

Global options: `--endpoint HTTPS_ORIGIN`, `--factory UUID`, `--project owner/repo`, `--id UUID`, `--request-id UUID`, `--file payload.json` (or `-` for stdin), and `--json`. Store scope with `mc config set --factory UUID --project owner/repo`; inspect it with `mc config show`. Arguments override stored defaults. `MC_ENDPOINT` overrides the stored endpoint.

| Commands | Scope and purpose |
|---|---|
| `status`, `capabilities` | Remote status and capability discovery |
| `factories list`, `factories show` | Accessible factories and project configuration |
| `bots list`, `ledger list` | Factory bot roster and activity |
| `execution show`, `execution set`, `execution verify` | Runtime connection configuration and verification |
| `records list`, `records set` | Revision-bound synchronized records |
| `runtime show`, `runtime set` | Project bot runtime |
| `schedules list`, `schedules set`, `schedules delete`, `schedules conflicts` | Project schedules and provider conflicts |
| `runs list`, `runs inspect --id UUID` | Execution history and bounded inspection |
| `tasks list`, `tasks submit` | Native project tasks |
| `handoffs submit`, `handoffs show --id UUID` | Explicit bot requests and durable receipts |
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

`--json` emits one object on stdout: `{"schemaVersion":1,"ok":true,"data":...}`. API responses remain under `data`; durable submissions wrap the receipt with `requestID` and `data`. Errors go to stderr as `{"schemaVersion":1,"ok":false,"error":{"code":4,"message":"API returned HTTP 403.","status":403}}`. Human output is indented JSON. Handoff/task persistence notices go only to stderr. Scripts must check the process exit status.

Exit codes: 0 success; 2 usage/local input or other HTTP rejection; 3 unauthenticated; 4 forbidden; 5 not found; 6 conflict; 7 network/timeout with potentially unknown acceptance; 8 rate limited; 9 upstream/server/invalid JSON response. Error bodies are not echoed because they may contain secrets.

Completion: `mc completion bash > ~/.mission-control-completion.bash`, then source that file from `.bashrc`. For zsh, save `mc completion zsh` as `_mc` in a directory on `fpath`, then run `compinit`. Completion currently covers top-level commands.
