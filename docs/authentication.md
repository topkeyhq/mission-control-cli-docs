# Authentication

Run `mc login` (or `mc login --browser` in a non-interactive terminal) for independent Google sign-in. No Mac pairing is required. `mc whoami` checks the stored identity and `mc logout` revokes it. `mc update` uses your GitHub login for private release downloads; that is separate from API authentication.

The default `observe` grant permits reads. Choose `--scope operate` for tasks, handoffs and session controls, or `--scope full` for configuration and worker registration. Grants expire after 30 days and never exceed your account's roles. Follow [independent sign-in](independent-signin.md) to verify your own GitHub membership when requested.

Existing device pairing remains supported with `mc auth login --pair-stdin`. `mc auth login --token-stdin` accepts an independently issued credential. Legacy tokens are 64 lowercase hexadecimal characters; expiring client grants have the `mcg1.` prefix. Neither accepts secrets in command arguments. Machine `mcw1.` credentials are managed separately by [the worker runner](workers.md).

Credentials live in `~/.config/mission-control/credentials.json`, with mode 0600. The containing directory is created with mode 0700. No Keychain API is called. This avoids repeated Keychain prompts but means anyone with access to your OS account can read the token. Use your OS account and disk protections. `MC_CONFIG_DIR` relocates local state; keep it private and outside repositories. POSIX permissions are tested on macOS; Windows ACL hardening has not been verified.

Credentials are bound to their API origin. Changing `--endpoint` cannot send a stored token to a different origin. `MC_TOKEN`, when explicitly supplied, is used with the selected origin. Only HTTPS origins are accepted, except localhost for development. Redirects are never followed.

`mc auth status` verifies the remote token. `mc auth logout` revokes the remote device before removing its local token. If revocation fails, the credential remains so you can retry. An environment token cannot be removed from its parent process: remove the CI secret or unset it after revocation. A 401 requires a new login; a 403 requires an appropriate project or organization role. Logging in does not elevate your role.

New installations default to `https://jsl.topkey.io`. Existing configured endpoints remain in effect. Stored credentials stay bound to their original origin; to explicitly move an existing CLI to JSL, sign in with `mc login --endpoint https://jsl.topkey.io`. Do not copy tokens or edit the credential file to bypass origin binding.

Home uses the same authenticated project read access as `mc runs list`. A missing login or project scope produces the existing setup error; `mc --help` remains available without authentication. Home never elevates roles or submits work.
