# Cognitive Seatbelt

An instructions-only Agent Skill for keeping human understanding in the loop during work with Codex, Claude Code, and other compatible agents.

Let the agent handle execution while you retain understanding of the goal, important decisions, tradeoffs, and outcomes. Cognitive Seatbelt adds short alignment checks, decision checkpoints, or comprehension questions to the task you are already doing.

Two independent controls determine **how much you participate** and **how much text you read**. Strict checkpoints can still use very short explanations.

## Install

Install the complete `skills/seatbelt` folder, including `references/checkpoints.md`. **Grill-Me (Grilling) and Caveman are not prerequisites**: Seatbelt implements its own questioning and communication rules. No scripts, packages, or MCP servers are required.

### Codex

Ask Codex:

```text
Use $skill-installer to install the seatbelt skill from
https://github.com/LunarianDev/cognitive-seatbelt/tree/main/skills/seatbelt
```

For manual installation, current Codex documentation lists `~/.agents/skills/seatbelt/` for personal use or `.agents/skills/seatbelt/` in a repository for project use. The Codex skill installer uses `$CODEX_HOME/skills/seatbelt`, defaulting to `~/.codex/skills/seatbelt`. Keep the supporting files alongside `SKILL.md` and avoid installing duplicate copies under the same name. See [Codex skills documentation](https://learn.chatgpt.com/docs/build-skills).

If `seatbelt` is already installed, review the existing folder before replacing it. The Codex skill installer stops rather than overwriting an existing skill.

### Claude Code

Copy `skills/seatbelt` to `~/.claude/skills/seatbelt/` for personal use or `.claude/skills/seatbelt/` in a repository for project use. Invoke it as `/seatbelt`. See [Claude Code skills documentation](https://code.claude.com/docs/en/skills).

`agents/openai.yaml` is optional Codex display metadata. Seatbelt's behavior is defined in `SKILL.md` and its bundled reference; other hosts do not need to interpret the Codex metadata.

### Other agents and harnesses

For a host that supports the [Agent Skills format](https://agentskills.io/specification), copy the complete folder into that host's supported skill location and use its invocation mechanism. Discovery locations and invocation syntax vary by host.

If a host has no skill loader, explicitly provide `SKILL.md` and `references/checkpoints.md` as task instructions. If it cannot read local files, include their text in the task context. The host must be able to retain conversational state and return a pending question to a human to support the interactive checkpoint workflow. Instructions remain subject to the host's own permissions and higher-priority rules.

## Use

In Codex, include the skill in a task prompt:

```text
$seatbelt strict --caveman Fix the session refresh race.
$seatbelt mentor --compact Help me learn this codebase while fixing the bug.
$seatbelt lite --normal Update the onboarding text.
```

In Claude Code, use the same modes and flags with `/seatbelt`:

```text
/seatbelt strict --caveman Fix the session refresh race.
/seatbelt mentor --compact Help me learn this codebase while fixing the bug.
```

You can also request it in natural language after making the skill available:

```text
Use Cognitive Seatbelt in strict mode with compact communication while fixing this bug.
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

In Claude Code, replace `$seatbelt` with `/seatbelt` in these control examples. You can also say **"skip this checkpoint"**. Changing only a communication flag does not release a pending comprehension check.

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
- Settings live in chat context. Handoff summaries preserve settings and pending checkpoints; if state is lost, the agent reports uncertainty and asks to restore it before dependent execution.
- In a noninteractive run, a required checkpoint returns a pending question and stops dependent execution until a human can resume. It does not silently pass or lower the selected mode.
- The skill does not change global preferences or require Grill-Me (Grilling) or Caveman. If a bundled reference is missing, the agent reports it and uses the core checkpoint rules in `SKILL.md`.
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

## Behavioral testing to run later

Cross-model and cross-harness behavioral testing is deferred. File-format validation does not establish that a model follows the protocol. When testing in fresh sessions, record the host, model, invocation, and observed behavior against these cases:

| Case | Expected behavior |
| --- | --- |
| Fresh activation without controls, through native invocation or natural language | Starts `standard --adaptive` and acknowledges the settings once. |
| Ordinary brevity or approval request without Seatbelt | Does not activate the protocol. |
| Strict checkpoint followed by "yes", then a relevant explanation | "Yes" alone leaves comprehension pending; an adequate explanation releases the boundary. |
| Pending checkpoint followed by `--compact`, explicit skip, or `off` | Communication alone does not release it; skip releases only that check; Off disables checkpoints. |
| Quoted controls or controls inside a task's command | Does not interpret task content as Seatbelt settings. |
| Caveman communication with no other skills installed | Preserves precise questions, evidence, and qualifiers without loading Grill-Me or Caveman. |
| Trivial edit; substantial task with a material decision | Avoids invented quizzes for trivial work and uses checkpoints at meaningful boundaries. |
| Context handoff with preserved state; continuation with state missing | Restores known state; reports unknown state and asks for restoration instead of assuming success. |
| One-shot run that reaches a required checkpoint | Returns the pending question and stops dependent execution, accurately reporting completed work. |
| Missing bundled reference | Reports the missing file and follows core checkpoint rules without fetching another skill. |

Try the applicable cases in Codex, Claude Code, and any other intended host. Include each model you plan to support, and keep observed results separate from expected behavior. See [Claude's authoring and testing guidance](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices).
