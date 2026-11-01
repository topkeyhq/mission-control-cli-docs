# Compatibility and coverage

Node.js 22+ CLI for the canonical League Cloud API, contract 1.3.0. Browser OAuth uses oauth4webapi; pairing and explicit token login remain supported. Tasks, handoffs, runtimes, schedules, session operations, worker registration/revocation, public transcripts, receipt metrics and restore-safe event checkpoints use the same server authority as Mac and web.

macOS/Linux CI runs Node 22/24. Live macOS verification covered independent login, restricted grants, real read-only Codex worker execution, shared receipt reads and revocation. Linux tests do not certify remote-host deployment. Windows worker execution is explicitly unsupported. Fresh organization setup and Omarchy/Windows installation remain separate acceptance work.

Metrics report durable dispatch receipts; cost, tokens and duration remain null. A completed worker result is not independent proof of task success. Old numeric-only event cursors remain accepted but need checkpoints for restore protection. No uncertain mutation is automatically replayed.

Source verification does not publish a package or upgrade existing installations. See [migration parity](migration-parity.md), [workers](workers.md), and [verification](verification.md).
