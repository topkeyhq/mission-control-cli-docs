# Cross-client migration coverage

CLI, Mac and web use the same canonical squad runtime, tasks, schedules, workers,
execution receipts, session operations, metrics and event checkpoints. The
[canonical matrix](https://github.com/topkeyhq/just-ship-league-mission-control-web/blob/main/docs/MIGRATION_PARITY.md)
records the entry point in each client, verification and remaining product gaps.

Worker registration and execution run on the host through the CLI; Mac and web
configure runtimes and inspect/revoke workers without transporting machine
credentials. A closed Mac does not stop the server scheduler or a standalone
worker. Windows workers are not supported.

Use `mc events list --since 0`, save both cursor and checkpoint with processed
history, then resume with both. On 409, explicitly rebuild observation history;
never replay tasks to repair a cache. Remove cached records on `run_removed`.
Use `mc metrics show` for shared dispatch counts, not native provider analytics.

Migration source and live API verification do not publish a new CLI package or
upgrade an installed Mac/browser. Full product/platform parity remains separate.
