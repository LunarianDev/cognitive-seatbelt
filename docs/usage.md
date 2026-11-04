# Controls and checkpoint behavior

For a first task and the mode tables, start with the [README](../README.md). The [skill instructions](../skills/seatbelt/SKILL.md) define the full behavior.

## Invocation and settings

Use the host's invocation mechanism:

```text
$seatbelt [off|lite|standard|strict|mentor] [--normal|--compact|--caveman|--adaptive] <task>
```

In Claude Code, use `/seatbelt` instead of `$seatbelt`. After installation, you can also ask in natural language:

```text
Use Cognitive Seatbelt in strict mode with compact communication while fixing this bug.
```

The first activation defaults to `standard --adaptive`. Later invocations change only the controls you supply. A bare invocation retains the current settings. `--help` and `--status` are informational; they do not activate or reset the protocol.

The agent should read controls adjacent to the invocation, not flags inside quoted material, code, or your task's commands. On one axis, the last repeated setting wins. An unknown control should prompt clarification rather than an invented mode.

## A checkpoint

The agent investigates available evidence, explains the proposed change and its consequence, then asks one focused question. It pauses the action that depends on your answer; independent preparation can continue.

In `standard`, a choice or short explanation can resolve a decision. In `strict` and `mentor`, the question asks for task-specific understanding. A correct multiple-choice answer suffices when recognition is the chosen check. Teach-back accepts the essential idea in your own words; exact terminology is unnecessary.

An incomplete answer gets a targeted follow-up. A misconception gets a short explanation and a reframed question. After two unsuccessful attempts, the agent should offer a short explanation and choices to retry, lower the mode, or skip. It should not shame you or produce a learner score.

## Skipping and stopping

Say `skip this checkpoint` to release only the current checkpoint. Use `$seatbelt off` to disable checkpoints while retaining the communication setting, or `$seatbelt off --normal` to return both controls to ordinary behavior. Use `/seatbelt` for those commands in Claude Code.

A communication-only change, such as `--compact`, does not release a pending checkpoint. A mode change reevaluates it under the new mode. An ordinary "go ahead" does not answer a pending strict comprehension question; an explicit instruction to bypass the check does.

## Keep interruptions proportional

Checkpoints belong at material decisions, deviations, and substantial handoffs. Routine formatting and already-agreed repetitive work normally need none, even in strict mode. The agent should reuse demonstrated understanding until the decision or its consequences change.

Compression affects conversational wording, not reasoning depth, technical precision, or deliverable content unless you request that too. Questions must remain natural and precise, including with `--caveman`.

## Handoffs and missing context

Settings live in the current chat rather than global preferences. A handoff should preserve the mode, communication setting, and any pending checkpoint's task, boundary, question, essential idea, and attempt count.

If that state is lost, the agent should consult available history, report what remains unknown, and ask you to restore the settings or resolve the checkpoint before dependent execution. It should not assume a checkpoint passed or silently reset the protocol. Canceling a task retires its checkpoint while retaining the two settings.

## Noninteractive work and permissions

When a required checkpoint cannot be answered in a one-shot run, the agent should return the pending question and stop dependent work. It should accurately distinguish completed work from work still waiting.

Comprehension and permission are separate. Passing a checkpoint grants no authority for deployment, deletion, spending, or other external actions. Seatbelt remains subject to the task's authorization and the host's higher-priority rules.

These are model instructions, not runtime enforcement. See [behavioral testing](behavioral-testing.md) for the scenarios that still need observed host and model results.
