# Independent CLI sign-in

Browser sign-in works without obtaining a pairing code from a Mac. The deployed API and source CLI were live-verified during the migration; package distribution is a separate release. Existing pairing/token login stays available.

An administrator registers a Google **Desktop app** OAuth client and configures
`GOOGLE_CLI_CLIENT_ID` and `GOOGLE_CLI_CLIENT_SECRET` on the canonical API.
Existing Mac/web audiences are preserved. The server exposes only the public
client ID; provider token exchange and the Desktop configuration stay server-side.
No OAuth configuration file is needed on the secondary laptop.

```sh
mc login --browser --scope observe
# Choose --scope operate to submit tasks and control sessions.
gh auth token | mc auth verify-github --github-stdin
mc whoami
```

Grants expire after 30 days and are independently revocable. `observe` is the
default. `operate` additionally allows task/handoff submission and session control.
Neither can pair more devices, read environment secrets, manage roles, or change
runtime configuration. `full` is an explicit choice and still cannot exceed the
user's existing roles. Unrecognized routes are denied to restricted clients.

The second command explicitly submits the GitHub account selected by `gh` to the
configured Mission Control origin for verification; it does not persist that
GitHub token. Check `gh auth status` first. GitHub CLI is optional: a secret manager
may provide a personal GitHub token on stdin instead. Never pass tokens as flags.
Repeat verification after the server's current proof expires. Failed verification
is an error; the CLI does not retry a consequential operation automatically.

Browser sign-in uses a random loopback port bound to 127.0.0.1, PKCE S256, random
state and nonce, and one matching callback. Only Google's fixed token endpoint is
used, redirects are rejected, and provider bodies are bounded. The canonical API
validates signature, audience, account admission and roles before issuing a
separate CLI device. Google access/ID tokens are not saved. The resulting device
credential uses the existing private atomic file storage and explicit logout.

Verification includes loopback protocol and canonical Worker/SQLite tests plus real Google sign-in, membership renewal, restricted-scope denial and revocation against the deployed API. See the [migration record](https://github.com/topkeyhq/just-ship-league-mission-control-web/blob/main/docs/SQUAD-MIGRATION.md) for exact evidence and rollout limits.

References: https://developers.google.com/identity/protocols/oauth2/native-app
and https://www.rfc-editor.org/rfc/rfc8252.html.

## Shared observation

After selecting a squad and project, `mc events list --since 0` reads durable
receipt changes. Save the returned cursor and checkpoint with processed events, then supply both
to `--since CURSOR --checkpoint HASH` on the next read. Continue while `more` is true. Remove cached run
history when receiving `run_removed`; never resubmit a task merely because the
connection was interrupted. A 409 requires a deliberate rebuild from zero.

`mc metrics show` reports dispatch-state counts with explicit provenance.
Uncollected cost, token usage and duration are null. `mc runs inspect --id UUID`
includes bounded, redacted public transcript text where available. Conductor transcripts
are live provider reads; worker results are stored public receipt text. Full transcript archival is unavailable; tools, reasoning and attachments
are omitted. Both commands work with an observation grant.
