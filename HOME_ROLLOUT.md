# Home briefing coordinated change

Classification: platform-adapted. Outcome: start with the next useful action, keep observation tools available, and move Insights into Settings.

- Mac: first icon-only Home tab; decisions, latest active PR follow-ups, finished work, releases; existing confirmation flows.
- iPhone: Home first; project-scoped PR follow-ups, uncertain local actions, offline status, observed daily merges; Insights/Activity under Settings.
- Web: Home first in project tools, default first authorized project; cached server receipt follow-ups and explicit inspect/start actions; Insights under Settings.
- CLI: `mc home` and no-command entry show read-only scoped receipt follow-ups; stable JSON summary; `mc insights` alias for metrics, existing commands preserved.
- No new endpoints, permissions, schema or automatic writes. Web/CLI counts describe the latest 100 accessible server receipts; Mac/iPhone describe observed board merges. Neither claims global history or independent completion proof.
- Tests: latest-active-run/project/day selection on Swift clients; shared receipt classification and CLI read-only behavior; browser navigation/offline and auth suites; simulator UI; each release uses its supported channel.
- Rollout: independently backward-compatible clients. Mac stable, CLI stable, web development preview; iPhone developer archive/simulator preview until signed distribution exists.

## Release evidence

Evidence and release URLs are recorded in the coordinating pull requests. Full cross-client product parity is not claimed by this Home presentation change. Existing iPhone signing and browser preview limitations remain.

| Surface | Target version/channel | Interaction evidence | Compatibility |
| --- | --- | --- | --- |
| Mac | 1.61.0 stable | Native Home preview; full Swift suite; Home model/parser tests | Existing board actions and confirmations |
| iPhone | 1.3.0 developer preview | Swift model tests, simulator build and UI acceptance | Existing recovery and PR actions; unsigned archive/simulator distribution |
| Web | 0.1.0-preview.10 | Preview verification including Chromium, Firefox, WebKit navigation/offline tests | Existing canonical read API; full-parity gate remains closed |
| CLI | 0.6.0 stable | Read-only HTTP command acceptance and canonical API integration | Existing JSON envelope; version 1 briefing data; explicit commands unchanged |

No new API, permissions, migrations, credential storage or retry mechanism is introduced. Home opens existing action/recovery flows. Offline or uncertain work is inspected before retry; summaries describe observed coverage only. Signed iPhone distribution and full browser/Mac feature parity remain outside this presentation release, with the existing repository release owners retaining those prerequisites.
