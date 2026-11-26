# Controls and checkpoint behavior

Start with the [README](../README.md) for the quick start and mode selector. The [skill instructions](../skills/seatbelt/SKILL.md) define the complete protocol.

## Invocation and settings

Use your host's invocation syntax:

```text
$seatbelt [off|lite|standard|strict|mentor] [--normal|--compact|--terse|--adaptive] <task>
```

In Claude Code, use `/seatbelt` instead of `$seatbelt`. `--caveman` is a full alias for `--terse`. You can also invoke Seatbelt in plain language:

```text
Use Cognitive Seatbelt in strict mode with compact output while fixing this bug.
```

How settings persist:
- First activation defaults to `standard --adaptive`, applying any controls you specify.
- Later invocations change only the controls you supply; a bare invocation keeps current settings.
- Plain `$seatbelt off` disables checkpoints **and** resets communication to `--normal`. To disable checkpoints while keeping short prose, pass the flag explicitly: `$seatbelt off --terse` or `$seatbelt off --compact`.
- `--help`, `--status`, and `--handoff` are informational. They do not activate, reset, or release a checkpoint.
- Controls are read adjacent to the invocation, never inside quoted text, code, or task commands. When the same axis appears twice, the last value wins. An unknown control prompts clarification instead of an invented mode.

## Inside a checkpoint

The agent finishes its planned preparation first (inspection, diagnostics, tests, or isolated drafts on disposable data), then asks **one** focused question at the end of the turn. It holds dependent production edits and writes to the task's database until the checkpoint resolves or is skipped, even when those edits are local and reversible.

Every paused turn ends with a clean footer carrying both active controls and the waiting boundary:

```text
Checkpoint (strict, --compact) | Paused before: app/migration.py, data/users.sqlite3
```

On the first checkpoint in a session (and after any missed attempt), the agent adds one short cue line:

```text
One sentence is enough. Reply "idk" for a hint, "explain", or "skip" to move on.
```

That cue is omitted on later checkpoints once you have seen it.

Checkpoint depth adapts to the task and to what you already know:
- If you state constraints, failure modes, or exact domain terms up front, Seatbelt skips textbook basics and checks blast radius, hidden assumptions, lock-in, or tradeoffs found during inspection.
- If you are learning the stack, Seatbelt translates jargon once in plain language and anchors questions in concrete examples from the task.
- Seatbelt avoids echo questions (never asking something whose answer was printed verbatim right above it) and avoids quoting its own `SKILL.md` rules at you.

## Replying, getting hints, and skipping

In `standard`, a choice or brief explanation resolves the boundary. In `strict` and `mentor`, the agent checks for the essential mechanism or consequence in your own words; exact terminology is unnecessary.

Natural short replies work at any checkpoint:
- **`idk` / `I don't know` / `hint`**: Gives a short hint or concrete anchor without penalty and asks a narrower version.
- **`explain`**: Explains the mechanism or tradeoff clearly, then lets you restate it in your own words right away, lower the mode, or skip.
- **`skip` / `skip this checkpoint`**: Releases only the current checkpoint without shaming or extra confirmation.
- **`lower to standard` / `switch to lite`**: Reevaluates the pending boundary under the new mode.
- **`$seatbelt off`**: Releases the checkpoint and returns both controls to ordinary behavior (or `$seatbelt off --terse` to keep terse prose).

After two unsuccessful attempts at the same checkpoint, the agent explains the missed point clearly and lets you answer immediately in your next reply, lower the mode, or skip. You do not need to send a separate `"retry"` turn first.

A communication-only change (such as `$seatbelt --compact`) does not release a pending checkpoint. A plain `"yes"` or `"go ahead"` can authorize task work where appropriate, but does not pass a pending `strict` or `mentor` comprehension question.

## Handoffs by mode

After substantial execution, the agent reports actual changes and verification results first, then closes the handoff according to mode:
- **`lite`**: No final comprehension check.
- **`standard`**: Closes clean execution with a short "what you now own" summary. Pauses for a handoff question only if execution materially diverged or uncovered a new risk or limitation.
- **`strict`**: Reuses earlier demonstrated understanding if it covers the actual change and its real consequences (briefly naming what it covered). Asks a final check when the initial check was skipped, execution diverged, a real caveat surfaced, or callers face an uncovered breaking change. It should never invent hypothetical future trivia just to ask a question.
- **`mentor`**: Closes with a short debrief takeaway, or one reflection question if a key consequence remained uncovered.

A skipped initial check supplies no demonstrated understanding for a later handoff.

## Keep interruptions proportional

Checkpoints belong at material decisions, deviations, and real risks. Mechanical formatting, literal text replacements, lookups, and agreed repetitive work normally need none, even in `strict`.

Compression affects conversational explanations, not reasoning depth, uncertainty, or deliverable content unless requested. Checkpoint questions and the paused footer stay in clear, natural language, including under `--terse` or `--caveman`.

## `--handoff`, continuation, and missing state

Settings stay in chat context; Seatbelt never writes global preferences. Run `$seatbelt --handoff` (or `/seatbelt --handoff`) before moving to a new chat or worktree to get a compact, copy-pasteable state block containing your active mode, communication flag, task, pending checkpoint, attempt count, answered checks, skipped checks, and completed vs. held work. See [references/handoffs.md](../skills/seatbelt/references/handoffs.md) for the exact format.

When you ask to continue a task that previously used Seatbelt, the agent loads the skill even without a fresh invocation. If prior state was lost, it inspects available history, reports what remains unknown, and asks you to restore settings and resolve uncertain boundaries before dependent work. It should not assume a checkpoint passed or silently reset to defaults. Canceling a task retires its checkpoint while keeping your two settings.

## Opt-in defaults via `AGENTS.md` or `CLAUDE.md`

Seatbelt never writes global config files or modifies your setup on its own. If you want Seatbelt active by default in one repository (or across your personal projects), add a short opt-in snippet to your project's `AGENTS.md` or `CLAUDE.md` (or your personal `~/.codex/AGENTS.md` / `~/.claude/CLAUDE.md`):

```markdown
## Cognitive Seatbelt default

For non-trivial code, schema, or architecture changes in this repository, use the
installed `seatbelt` skill in `standard --adaptive` mode (or `strict --compact` for
database migrations and auth changes) unless I explicitly turn it off with `off`.
```

Adjust the mode and scope to match the repo. Remove the block whenever you want Seatbelt back to explicit invocation only.

## Noninteractive runs and permissions

When a required checkpoint cannot be answered in a one-shot run, the agent stops dependent execution, reports completed preparation accurately, and ends its final message with the pending question and the two-control `Checkpoint (<mode>, <--flag>) | ...` footer so final-message-only outputs stay self-contained.

Comprehension and permission remain separate. Passing a checkpoint never authorizes deployment, deletion, external messages, spending, or other gated actions. Seatbelt stays subordinate to the task's authorization rules and the host's higher-priority constraints.

If a bundled reference (`references/checkpoints.md` or `references/handoffs.md`) cannot be read, the skill instructs the agent to name the missing file in its next user-facing reply and follow the core rules in `SKILL.md`.

These are model instructions, not runtime enforcement. See [behavioral testing](behavioral-testing.md) for expected scenarios and [recorded v0.1.0 trials](trials/2026-11-04/README.md) for observed host behavior.
