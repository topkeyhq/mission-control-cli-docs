# Independent workers

An administrator signs into the CLI with a full client grant and registers a
worker for one squad/project. Worker credentials are separate from the user's
credential, expire after 30 days, and cannot read records, provider secrets, or
manage other workers. Revocation or loss of the registering account's project
management role stops future authenticated worker requests.

```sh
mc worker register --factory SQUAD_UUID --project owner/repo \
  --name 'Build host' --kind local --capabilities codex
mc worker run --root /absolute/checkout --adapter /absolute/trusted-adapter
```

Use a separate `MC_CONFIG_DIR` for each worker. Registration saves its credential
in a private file and prints only metadata. `workers list` inspects registrations;
`workers revoke --id UUID` revokes one. To register a hosted machine, use
`--kind hosted` on that host. Both kinds use the same lease and fencing protocol.
The runner is a standalone process; the Mac application need not be running.
An operator may supervise it with their existing service manager. This release
supports macOS and Linux process groups; Windows execution is explicitly rejected.

Choose `executor: worker`, `workerKind: local|hosted` in runtime settings. Jobs
wait for a worker advertising the selected agent. The existing Conductor executor
remains available. Shared provider credentials are never included in worker jobs.

The locally chosen adapter receives one JSON object on stdin with `schemaVersion`,
`runID`, `repository`, `bot`, `agent`, `model`, `effort`, `prompt`, and `input`.
The server cannot choose the executable or its arguments. The checkout's GitHub
origin must match the registered project. The adapter runs with that checkout as
its working directory; this is **not an OS sandbox**. Use a trusted adapter that
enforces the filesystem and operation limits appropriate for its agent. The
runner passes a minimal environment, excluding ambient API tokens and SSH agent
access. The adapter must obtain any approved host-local credentials itself and
must return public text only.

The adapter must exit successfully and emit exactly one bounded JSON object:

```json
{"state":"completed","text":"Public result and verification evidence"}
```

Other accepted states are `failed` and `cancelled`. The runner discards stderr,
bounds stdout, and redacts recognized credential formats and known environment
secrets. This is a secondary safeguard, not a license to emit credentials.

A claim is not permission to execute until `start` succeeds. The runner renews
its lease every 30 seconds and terminates its process group on cancellation or
renewal failure. A server-side cron expires leases even with all clients closed.
An expired unstarted lease may be reclaimed; an expired running lease remains
uncertain. No automatic restart of a possibly executed job occurs.

A private local receipt is persisted before starting the adapter and before
reporting its result. A lost completion response can be recovered with
`mc worker recover`, which resends the same result and fencing number, never
reruns the executable. If execution stopped without a saved result, or the lease
expired, inspect the canonical run and reconcile the outcome before another task.
The runner refuses to continue past an unresolved local receipt.

## Bundled Codex adapter

Install and sign into the official Codex CLI on the worker host. Then use
`mc worker run --root /absolute/checkout --adapter codex`. The bundled adapter
uses non-interactive Codex with a read-only sandbox, no shared daemon, ephemeral
session storage, no user configuration overrides, and no approval escalation.
Only its final public response is retained. A runtime-specified model and effort
are passed explicitly; otherwise Codex's built-in default applies.

For deliberate local editing, the operator may add `--workspace-write`. This
choice is local to the runner; server task text cannot enable it. The adapter
keeps shell network access disabled and does not publish messages or mutate
remote systems. Existing hosted Conductor workflows retain their configured
provider integration behavior.

References: [official non-interactive documentation](https://learn.chatgpt.com/docs/non-interactive-mode)
and [official bounded repair-loop example](https://developers.openai.com/cookbook/examples/codex/build_iterative_repair_loops_with_codex).
The installed CLI's help was also checked for the exact supported flags.
