# Handoffs, continuation, and one-shot runs

Read this reference when generating a `--handoff` block, restoring state on a continuing task, or returning a paused checkpoint in a noninteractive run.

## Generating `--handoff`

`--handoff` (and natural-language requests for a Seatbelt handoff) is informational. It does not activate Seatbelt, change settings, or release a pending checkpoint.

Output a compact, copy-pasteable block in plain Markdown so the user can paste it directly into a new chat or worktree session:

```text
Seatbelt handoff
- Mode: <off|lite|standard|strict|mentor|inactive|unknown>
- Communication: <--normal|--compact|--terse|--adaptive|unknown>
- Task: <short concrete task description>
- Pending checkpoint: <none, or boundary + verbatim question>
- Essential idea: <none, or core concept being checked>
- Attempts: <0|1|2 or n/a>
- Demonstrated understanding: <brief list of answered checks, or none>
- Skipped checks: <brief list of skipped boundaries, or none>
- Completed work: <inspected facts, tests, or files actually changed>
- Still waiting: <production files, migrations, or handoff checks still held>
```

Keep answered checks and skipped checks strictly separate. Never count an agent explanation, plain approval, or skipped check as demonstrated understanding.

## Resuming and lost-state recovery

When a user continues work from a handoff block or a prior Seatbelt session:

1. **Restore explicit state first.** Read the mode, communication flag, task, pending checkpoint, attempt count, demonstrated understanding, and skipped checks from the pasted handoff or available chat history.
2. **Hold pending boundaries.** If the handoff lists an unresolved checkpoint, keep its dependent work paused until the user answers, lowers the mode, skips, or turns Seatbelt off. Do not re-run a checkpoint that is already recorded as answered unless the plan or consequences changed.
3. **Handle relocated paths cleanly.** If the workspace path changed (for example, a new worktree or fresh copy), verify the actual files on disk before resuming, without discarding the recorded checkpoint state.
4. **Never guess missing state.** If the user says the task previously used Seatbelt and prior settings or checkpoint status are unavailable:
   - State clearly which items are unknown (mode, communication flag, whether a checkpoint was pending or answered).
   - Hold dependent execution while state is unknown.
   - Ask the user to restore settings and confirm any uncertain boundary before continuing.
   - Never treat missing state as `inactive`, assume an earlier check passed, or silently reset an active task to `standard --adaptive`. (Only a genuinely fresh activation uses the defaults.)
5. **Retire canceled checkpoints.** If the user cancels or replaces the task, clear the pending checkpoint for the old task while keeping the active mode and communication flag.

## Noninteractive and one-shot runs

When Seatbelt reaches a required checkpoint in a closed-stdin or one-shot run where no user reply can arrive:

1. Stop all dependent execution before crossing the boundary. Independent preparation (inspection, diagnostics, disposable-data drafts, or regression tests) may be reported accurately.
2. Separate completed preparation from held work clearly. Never imply a held edit or unrun verification happened.
3. Ask the checkpoint question once at the end of the final message.
4. End the final message with the standard two-control paused footer so consumers that read only the last message retain both controls and the waiting boundary:

```text
Checkpoint (<mode>, <--flag>) | Paused before: <boundary>
```

Or when execution finished and a final handoff check remains:

```text
Checkpoint (<mode>, <--flag>) | Execution complete; handoff pending
```

Do not fabricate an answer, wait indefinitely, or silently lower the mode to finish the run. Explicit user instructions in the prompt to skip a checkpoint or turn Seatbelt off still apply.
