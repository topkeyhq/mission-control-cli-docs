# Production verification — October 31, 2026

The published v0.2.1 GitHub Release archive was downloaded independently, its SHA-256 checksum verified, and installed into a fresh npm prefix. The actual `mc update` path upgraded 0.2.0 to 0.2.1, verifying the downloaded archive checksum before installation. The user’s interactive `mc login` and subsequent `mc whoami` also succeeded against production.

That downloaded executable then used a disposable, independently paired device against the real production League Cloud API. Verified: pairing/login, file-persisted authentication, whoami, factories, bots, runtimes, schedules, runs, tasks and health reads, remote logout and subsequent authentication rejection. The temporary device was revoked. No bot launches or provider mutations were performed.

Separate installed-package integration tests use real HTTP, the canonical Worker and migrated SQLite with simulated provider responses. They verify record revision conflicts, task/session mutation and exact replay, readonly role denial, network-ambiguity recovery and credential origin isolation. These mutation tests are not claims of live provider execution.

Bellman’s organization-owned announcement workflow posted v0.2.1 in the shared release channel. A duplicate announcement-job rerun succeeded through the existing-receipt path, with one unchanged production receipt and no second post. [Release workflow evidence](https://github.com/topkeyhq/just-ship-league-mission-control-cli/actions/runs/41265570612). Each internal project opts into the shared organization bot/channel through Organization → Projects.

Linux/macOS CI tests run on Node 22 and 24. Windows, Omarchy, fresh organization-admin onboarding and every capability-parity scenario remain outside this verification.

## Independent access and workers — November 1, 2026

Later source verification completed independent Google browser login, scoped-grant
rejection and revocation, live read-only Codex execution in a private verification
project, checkpoint resume, worker revocation and hosted-kind lease-loss fencing.
These changes have not been published as a new package by this verification.
See the canonical [migration record](https://github.com/topkeyhq/just-ship-league-mission-control-web/blob/main/docs/SQUAD-MIGRATION.md).

The parity follow-up ran all 15 CLI tests with the updated canonical API source;
no cases were skipped. The guides now describe browser login, workers, metrics
and checkpoints, and completion includes their commands. See [migration parity](migration-parity.md).
