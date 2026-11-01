# Real workflows

## Ask a specific bot to review a PR

Set your factory and repository, then save a payload:

```json
{"bot":"rollie","pr":42,"instructions":"Review the retry handling.","expected_head":"0123456789abcdef0123456789abcdef01234567"}
```

Use the PR's actual full head SHA. Run `mc handoffs submit --file review.json --json`. The CLI generates a UUID if absent and saves the entire request to its private `requests` directory **before** contacting the server. Save the returned `requestID` with your work item.

This is an explicit handoff: it requests the configured bot independently of pipeline order, predecessor reviews, schedule eligibility, or automatic routing labels. Project permission, bot identity and target verification still apply. It does not authorize launching a chain of bots.

After a timeout, inspect with `mc handoffs show --id UUID`, or replay exactly with `mc requests replay --id UUID`. Never submit a fresh UUID merely because the first response was lost. The server rejects reuse of a UUID with different content. Local history binds the saved request to its origin and scope. Protect and retain this history until acceptance is resolved.

## Launch and inspect a native task

```sh
printf '%s' '{"bot":"YOUR_CONFIGURED_BOT","prompt":"Investigate the failing build and report findings."}' > task.json
mc tasks submit --file task.json --json
mc runs list --json
mc runs inspect --id RUN_UUID --json
```

Use a bot from the project's roster. A task receipt and execution lifecycle are distinct: an agent-reported blocker does not necessarily mean the transport failed. Inspection may include provider messages; handle the output as project-sensitive data.

## Configure scheduled work

Read `mc schedules list`, `mc runtime show`, and `mc schedules conflicts` first. Create a schedule payload matching the API contract, including its stable id, configured bot, prompt, intervalMinutes, enabled state and relevant anchor/time zone fields. Apply it with `mc schedules set --file schedule.json`. To pause, preserve the schedule fields and set `enabled` to false. To delete, use `mc schedules delete --file delete.json` with `{"id":"SCHEDULE_UUID"}`. Conductor conflicts may require pausing the existing schedule in Conductor.

## Inspect configuration safely

Use `mc factories show --json`, `mc health --json`, and `mc execution show --json` before changes. Use revision-aware reads and writes for records. Scope commands to one project; this CLI does not continuously scan or poll all projects. `mc metrics show` reads shared dispatch counts; `mc events list --since CURSOR --checkpoint HASH` resumes receipt history. These do not reproduce native historical provider analytics. Check deployed capabilities before using a newly added command.

## Steer an existing run

```sh
printf '%s' '{"content":"Please focus on the timeout regression."}' > message.json
mc sessions message --id RUN_UUID --file message.json --json
printf '%s' '{}' > action.json
mc sessions cancel --id RUN_UUID --file action.json --json
mc operations show --id REQUEST_UUID --json
```

The CLI persists a separate `requestID` before each control action. Supply `--request-id UUID` to make it explicit; `--id` identifies the run. Replay with `requests replay --id REQUEST_UUID`. A receipt state of `uncertain` means the provider may have accepted the action. The server returns the original receipt without resending an uncertain action. Investigate the run; never create a new action just to clear uncertainty. Archive and unarchive use the same pattern. Only server-owned, verified provider sessions are exposed.
