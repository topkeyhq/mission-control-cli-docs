# Compatibility, releases and troubleshooting

## 0.1.0

Initial Node 22+ CLI adapter for the canonical API migrated from Mission Control Mac v1.47.10. Supports device pairing and token login, scoped configuration, runtime/schedules, tasks, direct handoffs, runs and inspection. It contains no runtime dependencies. The API remains the authority for authorization and supported operations.

Verified: subprocess integration against a loopback HTTP server, credential file permissions on macOS, request persistence before HTTP submission, exact replay, scoped path generation, secret-safe errors, authorization/conflict/rate-limit exit codes, and device revocation. A second integration test runs the CLI against the canonical worker and migrated SQLite, verifying session action idempotency and readonly role denial with a mocked provider. These tests do not establish live production parity. Linux, Windows, Omarchy and fresh organization administrator onboarding have not been live-tested. Session message/cancel/archive/unarchive operations use the canonical 1.0.0 contract and require migration 0026. Cached Insights still awaits a canonical remote endpoint. Interactive browser OAuth is not included; pair through an existing authorized app session.

Install or update with `npm install --global ./topkeyhq-mission-control-cli-VERSION.tgz` from a verified release asset. Compare its SHA-256 digest to the release checksum before installing. `npm uninstall --global @topkeyhq/mission-control-cli` removes executables but retains private local state for recovery. Revoke with `mc auth logout` before intentionally removing credentials.

| Symptom | Action |
|---|---|
| 401 | Pair a new independent device or replace the expired/revoked CI secret |
| 403 | Ask an administrator for the necessary project role; do not retry with another identity |
| 404 for capability/operation | Check deployed API version and origin; the web UI origin is not necessarily the API origin |
| 409 | Read current revision or inspect existing request receipt before changing anything |
| Network error after submission | Reuse saved UUID via `requests replay`; do not create another request |
| Logout fails offline | Reconnect and retry remote revocation; local credential is intentionally retained |
| Invalid endpoint | Use an HTTPS origin without path, username, query or fragment |
| Cannot read JSON file | Check file contents and permissions; never paste credential files into bug reports |

Report CLI version, command name, HTTP status and exit code. Remove tokens, payloads, identities, paths and private repository information from public reports.
