# Automation

Inject `MC_TOKEN` and `MC_ENDPOINT` through your runner's secret store. Give each automation its own revocable device identity and only the roles required. Disable shell tracing around secrets.

```sh
set -eu
mc health --factory "$FACTORY_ID" --project "$REPOSITORY" --json > health.json
jq -e '.ok == true' health.json
mc runs list --factory "$FACTORY_ID" --project "$REPOSITORY" --json > runs.json
```

For submissions, generate and persist your request UUID with your job record before invocation. Use it in the JSON payload or `--id`. Preserve `MC_CONFIG_DIR` securely between retries, or preserve the exact payload and UUID in your job's private state. Ephemeral CI storage without persistent request IDs cannot safely distinguish timeout from rejection. Retry code 7 only with the same saved UUID; inspect first when possible. Code 6 means stop and reconcile conflicting state. Code 8 means back off; the CLI never automatically replays mutations.

Use `mc api GET /v1/capabilities --json` to verify compatibility before requiring newer operations. The raw API command only accepts `/v1/` paths and retains all authentication, timeout, redirect and output rules. Raw POST requests to task/handoff collections receive the same durable persistence treatment.

Avoid high-frequency polling. Fetch one scoped project on demand. Do not place provider credentials, runtime secret values, access tokens or inspection output in public CI artifacts.
