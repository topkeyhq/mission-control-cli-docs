# Authentication

Each engineer pairs their own device using an authorized Mission Control session. The CLI never imports another engineer's identity. Obtain a pairing code through your existing app's device pairing flow, then pipe it to `mc auth login --pair-stdin`. A pairing code is exchanged at `/v1/devices/pair`; the issued token is checked with `/v1/status` before saving. If your organization uses Google onboarding, finish onboarding in Mission Control first. Interactive browser OAuth is not implemented in this initial CLI.

`mc auth login --token-stdin` accepts an independently issued device token. Neither mode accepts a secret as an argument. Both expect exactly 64 lowercase hexadecimal characters. Use a password manager's stdout command or your CI secret store, with shell tracing disabled.

Credentials live in `~/.config/mission-control/credentials.json`, with mode 0600. The containing directory is created with mode 0700. No Keychain API is called. This avoids repeated Keychain prompts but means anyone with access to your OS account can read the token. Use your OS account and disk protections. `MC_CONFIG_DIR` relocates local state; keep it private and outside repositories. POSIX permissions are tested on macOS; Windows ACL hardening has not been verified.

Credentials are bound to their API origin. Changing `--endpoint` cannot send a stored token to a different origin. `MC_TOKEN`, when explicitly supplied, is used with the selected origin. Only HTTPS origins are accepted, except localhost for development. Redirects are never followed.

`mc auth status` verifies the remote token. `mc auth logout` revokes the remote device before removing its local token. If revocation fails, the credential remains so you can retry. An environment token cannot be removed from its parent process: remove the CI secret or unset it after revocation. A 401 requires a new login; a 403 requires an appropriate project or organization role. Logging in does not elevate your role.
