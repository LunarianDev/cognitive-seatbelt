# Cognitive Seatbelt

A Codex skill for keeping human understanding in the loop during agent work.

Let the agent handle execution while you retain understanding of the goal, important decisions, tradeoffs, and outcomes. Cognitive Seatbelt adds short alignment checks, decision checkpoints, or comprehension questions to the task you are already doing.

Two independent controls determine **how much you participate** and **how much text you read**. Strict checkpoints can still use very short explanations.

## Install

Ask Codex:

```text
Use $skill-installer to install the seatbelt skill from
https://github.com/LunarianDev/cognitive-seatbelt/tree/main/skills/seatbelt
```

Alternatively, copy the complete `skills/seatbelt` folder into `$CODEX_HOME/skills/seatbelt`, or `~/.codex/skills/seatbelt` if `CODEX_HOME` is unset. Keep `agents/` and `references/` alongside `SKILL.md`.

If `seatbelt` is already installed, review the existing folder before replacing it. The Codex skill installer stops rather than overwriting an existing skill.

## Use

Include the skill in a task prompt:

```text
$seatbelt strict --caveman Fix the session refresh race.
$seatbelt mentor --compact Help me learn this codebase while fixing the bug.
$seatbelt lite --normal Update the onboarding text.
```

The mode and flags are prompt instructions interpreted by the agent, rather than a shell command or executable argument parser.

### Cognitive modes

| Mode | Participation |
| --- | --- |
| `off` | No Seatbelt checkpoints. |
| `lite` | One brief alignment check for meaningful work; revisit it if scope materially changes. |
| `standard` | Alignment plus checkpoints at major decisions, deviations, and substantial handoffs. |
| `strict` | Demonstrate task-specific understanding before crossing those boundaries. |
| `mentor` | Strict checkpoints plus prediction, teach-back, error spotting, or test design. |

### Communication flags

| Flag | Communication |
| --- | --- |
| `--normal` | Ordinary clear prose. |
| `--compact` | Short sentences; essential facts first. |
| `--caveman` | Very terse explanations, retaining necessary detail and uncertainty. |
| `--adaptive` | Start compact; expand when requested or needed for understanding. |

First activation defaults to **`standard --adaptive`**. Settings persist within the chat. Later invocations change only the controls you supply:

```text
$seatbelt --compact       # Change communication, retain cognitive mode.
$seatbelt strict          # Change cognitive mode, retain communication.
$seatbelt --status        # Show settings and any pending checkpoint.
$seatbelt --help          # Show usage without activating the protocol.
$seatbelt off             # Disable checkpoints, retain communication.
$seatbelt off --normal    # Return both controls to ordinary behavior.
```

You can also say **"skip this checkpoint"**. Changing only a communication flag does not release a pending comprehension check.

## What a checkpoint looks like

For a task involving concurrent session refresh requests:

> Two requests can compete to rotate the same token. Proposed fix: serialize refresh per session. Tradeoff: simultaneous requests may wait briefly.
>
> What does serializing refresh requests prevent?
>
> A. Tokens reaching their expiration time.
> B. Two requests competing to rotate the same token.
> C. Every possible authentication failure.

Questions target the actual decision. Multiple-choice distractors represent plausible misunderstandings; teach-back asks you to explain a consequence in your own words. Even with `--caveman`, questions remain natural and precise.

The agent investigates available evidence first, asks one focused question at a time, and pauses the action that depends on the answer. Independent preparation can continue. A misconception prompts a short explanation and another attempt, with options to retry, lower the mode, or skip after two unsuccessful attempts.

## Boundaries

- Checkpoints occur at meaningful decisions, rather than every tool call. Mechanical formatting and already-agreed repetitive work normally need none.
- Compression changes conversational wording, not reasoning depth, technical precision, or the content of deliverables unless requested.
- Passing a question establishes the selected checkpoint; it does not grant permission for external actions.
- Settings live in chat context. The skill does not change global preferences or depend on the separate Caveman skill.
- This version uses model instructions. It has no runtime hook that blocks tools, and it makes no claim of measured learning benefits or token savings.

## Skill files

```text
skills/seatbelt/
  SKILL.md                    Protocol, modes, flags, and checkpoint rules.
  agents/openai.yaml           Codex display metadata and starting prompt.
  references/checkpoints.md    Question patterns and evaluation guidance.
```

Read the [skill instructions](skills/seatbelt/SKILL.md) or the [checkpoint guidance](skills/seatbelt/references/checkpoints.md) for the full behavior.

Feedback from real tasks is welcome, especially examples where a checkpoint helped, interrupted unnecessarily, or became unclear after compression. Use sanitized task descriptions and exclude private code or credentials.
