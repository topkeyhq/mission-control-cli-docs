# Compatibility and coverage

Node.js 22+ CLI for the canonical League Cloud API. Browser OAuth uses oauth4webapi; pairing and explicit token login remain supported. Tasks, one-time and pipeline handoffs, runtimes, schedules, session operations, worker registration/revocation, public transcripts, receipt metrics and restore-safe event checkpoints use the same server authority as Mac and web. Pipeline handoffs require the server's explicit `features.manualHandoffModes` capability; releases verify that production flag before publishing.

macOS/Linux CI runs Node 22/24. Live macOS verification covered independent login, restricted grants, real read-only Codex worker execution, shared receipt reads and revocation. Linux tests do not certify remote-host deployment. Windows worker execution is explicitly unsupported. Fresh organization setup and Omarchy/Windows installation remain separate acceptance work.

Metrics report durable dispatch receipts; cost, tokens and duration remain null. A completed worker result is not independent proof of task success. Old numeric-only event cursors remain accepted but need checkpoints for restore protection. No uncertain mutation is automatically replayed.

Source verification does not publish a package or upgrade existing installations. See [migration parity](migration-parity.md), [workers](workers.md), and [verification](verification.md).

Workflow commands require API 1.4.0 / Web v0.1.0-preview.3 and full client grants. The generic worker is separate from `evaluations worker`, which requires Python 3, Git, HTTPS and disposable trusted infrastructure. See [workflow boundaries](shared-workflows.md).

CLI 0.6.0 adds Home as the no-command entry and an Insights alias. Existing named command and JSON-envelope behavior is preserved. Home uses existing scoped `/runs` reads; no server upgrade or new permission is required. Its coverage differs from Mac/iPhone observed PR counts and is described in every output.
