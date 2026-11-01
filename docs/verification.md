# Production verification — October 31, 2026

The published v0.2.0 GitHub Release archive was downloaded independently, its SHA-256 checksum verified, and installed into a fresh npm prefix. Its `mc update` command successfully downloaded and verified the stable release and reinstalled it.

That downloaded executable then used a disposable, independently paired device against the real production League Cloud API. Verified: pairing/login, file-persisted authentication, whoami, factories, bots, runtimes, schedules, runs, tasks and health reads, remote logout and subsequent authentication rejection. The temporary device was revoked. No bot launches or provider mutations were performed.

Separate installed-package integration tests use real HTTP, the canonical Worker and migrated SQLite with simulated provider responses. They verify record revision conflicts, task/session mutation and exact replay, readonly role denial, network-ambiguity recovery and credential origin isolation. These mutation tests are not claims of live provider execution.

Bellman's project-owned release-announcement workflow is being connected and verified separately; an announcement job is not considered verified until production confirms its receipt.

Linux/macOS CI tests run on Node 22 and 24. Windows, Omarchy, fresh organization-admin onboarding and every capability-parity scenario remain outside this verification.
