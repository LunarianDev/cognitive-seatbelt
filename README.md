# Cognitive Seatbelt

**Let AI do the work. Don't outsource your understanding.**

A lightweight Agent Skill that keeps you in charge of the decisions that matter when building software with AI agents.

[Quick start](#quick-start) · [See it in action](#see-it-in-action) · [Pick your mode](#pick-your-mode) · [Documentation](#documentation)

## Why use it?

An agent can investigate a bug, edit your code, and run tests before you have worked out why it picked that fix. You can end up approving changes you cannot explain or maintain.

Cognitive Seatbelt adds short check-ins to the task you are already doing. The agent investigates first, explains the real decision or tradeoff, and pauses according to your chosen mode before crossing into production edits or data changes.

Use it when you are learning a codebase, working in an unfamiliar stack, or delegating changes whose consequences you want to own. Routine formatting and agreed repetitive edits usually need no checkpoint.

Two independent controls let you choose **how much you participate** and **how much you read**. Deep checks can still use short explanations.

No scripts, packages, servers, or other skills are required. Seatbelt is a folder of instructions in the [Agent Skills format](https://agentskills.io/specification).

## See it in action

Instead of receiving a finished patch with a wall of diffs, you check the real risk before the code changes.

*Illustrative dialogue, not a recorded test run. The appropriate fix and tests depend on the application.*

```text
You:    $seatbelt strict --compact Fix concurrent session refresh failures.

Agent:  Seatbelt active: strict, --compact.

        Two parallel requests from the same tab hit /refresh with the same
        token. The first rotates it; the second gets 401 and logs the user out.

        Proposed fix: serialize refreshes per session with an in-memory lock
        and keep the prior token valid for a 5-second grace window so waiting
        requests reuse the new session.

        Tradeoff: a stolen refresh token stays valid for 5 seconds, and the
        lock only coordinates requests inside one process.

        If we run two API instances behind a load balancer later, what happens
        with this fix?

        Checkpoint (strict, --compact) | Paused before: auth/refresh.ts
        One sentence is enough. Reply "idk" for a hint, "explain", or "skip" to move on.

You:    Requests hitting different instances won't share the lock, so the race
        comes back unless we move the lock to Redis or the DB.

Agent:  Right. Implementing the in-memory lock and adding a concurrent-request test.

        [Implements auth/refresh.ts and runs the test suite.]

        Done. Refreshes serialize per session with a 5-second grace window;
        all 14 tests pass, including the parallel-request test. One detail
        changed during implementation: lock timeouts return 503 instead of 401
        so clients retry rather than clearing the session.

        Your earlier answer covered the single-instance limit. Before closing:
        what does returning 503 instead of 401 mean for a client that only
        retries on network errors?

        Checkpoint (strict, --compact) | Execution complete; handoff pending

You:    It won't log the user out, but the request will still fail unless the
        client retries 503s too.

Agent:  Right. Handoff complete.
```

The agent investigates first, avoids trivia, and asks about an applied consequence instead of repeating what it just printed.

During any checkpoint you can reply `"idk"` or `"hint"` for a nudge, `"explain"` to unpack the answer right away, `"skip"` to move past that check, or `"lower to standard"` to dial back friction.

## Quick start

### 1. Install

Install the complete [`skills/seatbelt`](skills/seatbelt) folder, including its references and license.

For **Codex**, ask its skill installer:

```text
Use $skill-installer to install the seatbelt skill from
https://github.com/LunarianDev/cognitive-seatbelt/tree/main/skills/seatbelt
```

For **Claude Code**, copy that folder to `~/.claude/skills/seatbelt/` (`%USERPROFILE%\.claude\skills\seatbelt\` on Windows) for personal use, or `.claude/skills/seatbelt/` inside a project, then start a fresh session and invoke `/seatbelt`.

See the [installation guide](docs/installation.md) for Windows paths, manual Codex setup, the optional `npx skills add` CLI, and updates.

### 2. Verify in 10 seconds

Start a fresh session and run:

```text
$seatbelt --help
```

(Use `/seatbelt --help` in Claude Code.) This confirms the host discovered the skill without activating checkpoints.

### 3. Add it to a task

```text
$seatbelt standard --adaptive Build a user settings page.
$seatbelt strict --normal Migrate the users table to require email.
$seatbelt mentor --normal Help me learn this auth flow while fixing the bug.
```

In Claude Code, replace `$seatbelt` with `/seatbelt`. A first activation without options defaults to `standard --adaptive`. Modes and flags are instructions read by the model, not a shell parser.

## Pick your mode

| Situation | Starting point | Why |
| --- | --- | --- |
| Quick tweak or routine refactor | `lite --compact` | One alignment check up front, then hands-off execution. |
| Everyday feature work | `standard --adaptive` | Checks major decisions and deviations; closes cleanly when nothing surprises you. |
| High-stakes DB, auth, or billing change | `strict --normal` | Holds production edits until you explain the risk or mechanism in your own words. |
| Learning a new codebase or stack | `mentor --normal` | Adds predictions, error spotting, and test design before revealing answers. |

### Participation modes

| Mode | Your involvement |
| --- | --- |
| `off` | No Seatbelt checkpoints. Plain `off` also resets prose to `--normal`. |
| `lite` | One alignment check for meaningful work; revisit only if scope materially changes. |
| `standard` | Alignment, checks at major decisions and deviations, and a brief "what you now own" close (pauses at handoff only on divergence or new risk). |
| `strict` | Demonstrate task-specific understanding before dependent work continues. |
| `mentor` | Strict checkpoints plus prediction, teach-back, error spotting, or validation design, ending with a short debrief. |

### Communication flags

| Flag | Explanation style |
| --- | --- |
| `--adaptive` | Brief first; expand when requested or needed for understanding. |
| `--normal` | Ordinary clear prose. |
| `--compact` | Short sentences; essential facts first. |
| `--terse` | Very terse wording, retaining precision and uncertainty. Alias: `--caveman`. |

Settings persist within the chat. Changing one control keeps the other, except plain `off`, which resets both controls to ordinary behavior:

```text
$seatbelt --compact       # Keep the mode; shorten explanations.
$seatbelt mentor          # Keep communication; deepen checkpoints.
$seatbelt --status        # Show active settings and any pending checkpoint.
$seatbelt --handoff       # Output a copy-pasteable state block for a new chat.
$seatbelt --help          # Show syntax, modes, flags, and reply shortcuts.
$seatbelt off             # Disable checkpoints and return to ordinary prose.
$seatbelt off --terse     # Disable checkpoints while keeping terse prose.
```

In Claude Code, replace `$seatbelt` with `/seatbelt`. Changing only a communication flag does not release a pending checkpoint. See [controls and checkpoint behavior](docs/usage.md) for details and opt-in `AGENTS.md` / `CLAUDE.md` snippets.

## Limits and testing status

Seatbelt is an instructions-only conversational protocol. It has no runtime hook that blocks tools. Passing a checkpoint does not authorize deployment, deletion, or external actions, and it does not prove that generated code is correct.

Behavior depends on the host, model, and retained chat context. Recorded trials cover both v0.1.0 ([November 4, 2026 report](docs/trials/2026-11-04/README.md), including human walkthroughs) and v0.2.0 scripted trials ([November 25, 2026 report](docs/trials/2026-11-25/README.md)) on Codex CLI `0.171.0` (`gpt-6-astra / high`) and Claude Code `2.1.330` (`claude-fable-5-2`). On v0.2.0, both hosts fixed duplicate mid-turn questions, wasted retry turns, and final-message `B12` settings, and Claude Code ran 19 turns with 0 UX audit findings; Codex CLI still occasionally leaks a `[Seatbelt](.../SKILL.md)` policy link before the paused footer (2 of 3 spot-check turns).

The [behavioral testing checklist](docs/behavioral-testing.md) separates expected behavior from observed results and defines scenarios for running fresh trials.

## Documentation

- [Installation](docs/installation.md): Codex, Claude Code, Windows paths, and verification.
- [Controls and checkpoints](docs/usage.md): reply shortcuts, state, handoffs, and opt-in defaults.
- [Skill instructions](skills/seatbelt/SKILL.md): the full protocol.
- [Checkpoint reference](skills/seatbelt/references/checkpoints.md): question design, calibration, and evaluation.
- [Handoff reference](skills/seatbelt/references/handoffs.md): `--handoff` template, recovery, and one-shot resume.
- [Behavioral testing](docs/behavioral-testing.md): expected scenarios and trial template.
- [Recorded v0.2.0 trials](docs/trials/2026-11-25/README.md): Codex CLI and Claude Code v0.2.0 cohort results and open caveats.
- [Recorded v0.1.0 trials](docs/trials/2026-11-04/README.md): v0.1.0 baseline trials and human walkthroughs.
- [Release checklist](docs/release-checklist.md): preparation record for v0.1.0.

## Contributing

Real-task feedback helps, especially where a checkpoint caught a real bug, interrupted unnecessarily, or felt confusing. [Open an issue](https://github.com/LunarianDev/cognitive-seatbelt/issues/new/choose) with the host, model, settings, and a sanitized example. Exclude private code, credentials, and client data.

See [CONTRIBUTING.md](CONTRIBUTING.md) for reporting and pull request guidance.

## License

[MIT](LICENSE). A copy is included in the installable skill folder.
