# Cognitive Seatbelt

**Let AI do the work. Don't outsource your understanding.**

A lightweight Agent Skill that helps you stay involved in the decisions that matter when building software with AI agents.

[Get started](#quick-start) · [See it in action](#see-it-in-action) · [Choose a mode](#choose-your-level-of-involvement) · [Documentation](#documentation)

## Why use it?

An agent can investigate a bug, change your code, and run tests before you've worked out why it chose that fix. You may end up approving work you can't explain or maintain.

Cognitive Seatbelt adds short check-ins to the task you're already doing. The agent explains a meaningful decision and, depending on your chosen mode, asks you to confirm the direction, make a choice, or check your understanding before continuing.

Use it when you're learning to build with AI, getting familiar with a codebase, or delegating work whose consequences you want to understand. Routine formatting and already-agreed repetitive work usually need no checkpoint.

Two independent controls let you choose **how much you participate** and **how much you read**. Deeper understanding checks can still use short explanations.

No scripts, packages, servers, or other skills are required. Seatbelt is a folder of instructions in the [Agent Skills format](https://agentskills.io/specification).

## See it in action

Instead of receiving only a finished fix, you get a chance to understand the decision behind it.

*Illustrative dialogue, not a recorded test run. The appropriate fix and tests depend on the application.*

```text
You:    $seatbelt strict --compact Fix concurrent session refresh failures.

Agent:  I found two requests competing to rotate the same token.
        I'll serialize refreshes per session.
        Tradeoff: simultaneous requests may wait briefly.

        Before I change the refresh flow, what does serialization prevent?

        A. Tokens reaching their expiration time.
        B. Two requests competing to rotate the same token.
        C. Every possible authentication failure.

You:    B

Agent:  Right. I'll implement the change and check simultaneous requests.

        [Implements the fix and runs the relevant tests.]

        Refreshes are now serialized per session. The concurrent-request
        check passed. This addresses competing refreshes, not every cause
        of login failure.

        Before handoff, what changed and what limitation should you remember?

You:    We serialize refreshes per session. Other login failures remain possible.

Agent:  Right. That completes the handoff.
```

The agent investigates first, then asks about the actual decision. In strict mode, a correct recognition answer or an adequate explanation releases that checkpoint. A plain "yes" doesn't answer a comprehension question.

You can say **"skip this checkpoint"** or turn checkpoints off at any time.

## Quick start

### 1. Install

Install the complete [`skills/seatbelt`](skills/seatbelt) folder, including its reference and license.

For **Codex**, ask its skill installer:

```text
Use $skill-installer to install the seatbelt skill from
https://github.com/LunarianDev/cognitive-seatbelt/tree/main/skills/seatbelt
```

For **Claude Code**, copy that folder to `~/.claude/skills/seatbelt/` for personal use, or `.claude/skills/seatbelt/` in a project. Invoke it with `/seatbelt`.

See the [installation guide](docs/installation.md) for manual Codex installation, updates, and other compatible agents.

### 2. Add it to a task

In Codex:

```text
$seatbelt standard --compact Build a user settings page.
```

In Claude Code:

```text
/seatbelt standard --compact Build a user settings page.
```

To spend more time learning:

```text
$seatbelt mentor --compact Help me learn this codebase while fixing the bug.
```

A first activation without options defaults to `standard --adaptive`. Modes and flags are instructions interpreted by the agent, not a shell command or executable argument parser.

## Choose your level of involvement

Participation determines when and how the agent checks in.

| Mode | Your involvement |
| --- | --- |
| `off` | No Seatbelt checkpoints. |
| `lite` | One alignment check for meaningful work; revisit it if scope materially changes. |
| `standard` | Alignment, then check-ins at major decisions, deviations, and substantial handoffs. |
| `strict` | Demonstrate task-specific understanding before dependent work continues. |
| `mentor` | Strict checkpoints plus prediction, teach-back, error spotting, or test design. |

Communication independently determines how much you read.

| Flag | Explanation style |
| --- | --- |
| `--adaptive` | Brief first; expand when requested or needed for understanding. |
| `--normal` | Ordinary clear prose. |
| `--compact` | Short sentences; essential facts first. |
| `--caveman` | Very terse wording, retaining precision and necessary uncertainty. |

Settings persist within the chat. Changing one control keeps the other:

```text
$seatbelt --compact       # Keep the mode; shorten explanations.
$seatbelt mentor          # Keep communication; deepen checkpoints.
$seatbelt --status        # Show settings and any pending checkpoint.
$seatbelt --help          # Show usage without activating the protocol.
$seatbelt off             # Disable checkpoints; retain communication.
$seatbelt off --normal    # Return both controls to ordinary behavior.
```

In Claude Code, replace `$seatbelt` with `/seatbelt`. Changing only a communication flag does not release a pending comprehension check. See [controls and checkpoint behavior](docs/usage.md) for more detail.

## Limits and testing status

Seatbelt is an instructions-only conversational protocol. It has no runtime hook that blocks tools. Passing a checkpoint does not authorize deployment, deletion, or another external action, and it does not establish that generated code is correct.

Behavior depends on the host, model, and retained chat context. In a noninteractive run, a required checkpoint should be returned as pending while dependent work stops. Recorded trials cover Codex CLI 0.166.0 with `gpt-6.1-sol / xhigh` and Claude Code 2.1.318 with `claude-opus-5-5 / medium`. The [trial report and sanitized transcripts](docs/trials/2026-11-04/README.md) distinguish the broader-rule candidate from the final disclosure change.

Known limits remain. Claude disclosed a missing reference late in one of three focused attempts. Both hosts returned complete pending settings in the output stream but omitted the communication setting from the final message alone. Keep the full output when resuming one-shot work. These observations establish neither general reliability nor learning benefits or token savings; the example above is illustrative.

The [behavioral testing checklist](docs/behavioral-testing.md) separates expected behavior from observed results and explains how to contribute a sanitized trial.

## Documentation

- [Installation](docs/installation.md): host locations, updates, and manual setup.
- [Controls and checkpoints](docs/usage.md): state, handoffs, and noninteractive work.
- [Skill instructions](skills/seatbelt/SKILL.md): the full protocol.
- [Checkpoint reference](skills/seatbelt/references/checkpoints.md): question design and evaluation.
- [Behavioral testing](docs/behavioral-testing.md): scenarios and trial records.
- [Recorded trials](docs/trials/2026-11-04/README.md): tested configurations, evidence, and known limitations.
- [Release checklist](docs/release-checklist.md): preparation for the first release.

## Contributing

Real-task feedback is useful, especially where a checkpoint helped, interrupted unnecessarily, or became confusing. [Open an issue](https://github.com/LunarianDev/cognitive-seatbelt/issues/new/choose) with the host, model, settings, and a sanitized example. Exclude private code, credentials, and client data.

See [CONTRIBUTING.md](CONTRIBUTING.md) for reporting and pull request guidance.

## License

[MIT](LICENSE). A copy is included in the installable skill folder.
