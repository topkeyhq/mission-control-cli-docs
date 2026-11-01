# End-to-end verification — October 31, 2026

Result: packaged CLI passes the exercised local end-to-end workflows; production readiness is not yet established.

Installed the versioned 0.1.0 archive into a fresh temporary npm global prefix. Executed that installed binary, rather than the source executable, through real loopback HTTP against the integration checkout's canonical Worker and full SQLite migration chain. Upstream Conductor responses and onboarding prerequisites were fixtures. No customer bot was launched.

Passed: own-device pairing and persisted credential authentication; factories/bots/runtime/schedules/runs/inspection reads; record write/read and stale-revision conflict; native task launch, receipt listing and replay with exactly one provider mutation; session cancellation, durable replay and receipt lookup with exactly one provider mutation; readonly mutation denial; logout and subsequent authentication failure. Supplemental HTTP tests cover handoff pre-submit persistence, changed-payload rejection, unknown network acceptance, exact replay, credential origin isolation, error codes and secret-safe errors.

Reproduce with Node 22+:

```sh
npm install --prefix /tmp/mc-cli-verify --global ./topkeyhq-mission-control-cli-0.1.0.tgz
MC_TEST_BIN=/tmp/mc-cli-verify/bin/mc MC_API_SOURCE=/path/to/mission-control-web/server npm test
```

All 3 test groups passed, 0 skipped, on macOS Node 22.15.1. The canonical test is explicitly skipped without MC_API_SOURCE; an ordinary npm test alone does not prove backend integration.

Live checks against https://league-cloud.bryan-7b6.workers.dev: invalid credentials correctly returned HTTP 401. However, the newly specified public `/v1/capabilities` also returned HTTP 401. Combined backend deployment is still pending. The CLI release is still a draft. Authenticated production operations and real provider execution are not certified by these checks. Integration must deploy the combined backend, publish the release and rerun live smoke checks before declaring production readiness.
