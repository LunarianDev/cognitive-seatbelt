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

Preparation can include diagnostics, tests, and drafts kept outside the production path using disposable data. Production edits implementing an unresolved material decision and writes to the task's database must wait, even when those edits are local and reversible.

In `standard`, a choice or short explanation can resolve a decision. In `strict` and `mentor`, the question asks for task-specific understanding. A correct multiple-choice answer suffices when recognition is the chosen check. Teach-back accepts the essential idea in your own words; exact terminology is unnecessary.

An incomplete answer gets a targeted follow-up. A misconception gets a short explanation and a reframed question. After two unsuccessful attempts, the agent should offer a short explanation and choices to retry, lower the mode, or skip. It should not shame you or produce a learner score.

## Skipping and stopping

Say `skip this checkpoint` to release only the current checkpoint. Use `$seatbelt off` to disable checkpoints while retaining the communication setting, or `$seatbelt off --normal` to return both controls to ordinary behavior. Use `/seatbelt` for those commands in Claude Code.

A communication-only change, such as `--compact`, does not release a pending checkpoint. A mode change reevaluates it under the new mode. An ordinary "go ahead" does not answer a pending strict comprehension question; an explicit instruction to bypass the check does.

A skip supplies no understanding for a later boundary. At a substantial handoff in Standard, Strict, or Mentor, the agent reports the result and validation, then either identifies an earlier answer that covers the actual change and consequence or asks about the missing understanding. A new limitation can require another check. An explicit skip of that final check or Off releases it; Lite requires no final comprehension check.

## Keep interruptions proportional

Checkpoints belong at material decisions, deviations, and substantial handoffs. Routine formatting and already-agreed repetitive work normally need none, even in strict mode. The agent should reuse demonstrated understanding until the decision or its consequences change.

Compression affects conversational wording, not reasoning depth, technical precision, or deliverable content unless you request that too. Questions must remain natural and precise, including with `--caveman`.

## Handoffs and missing context

Settings live in the current chat rather than global preferences. A handoff should preserve the mode, communication setting, any pending checkpoint's task, boundary, question, essential idea, and attempt count, plus the understanding already demonstrated. Answered and skipped checks are separate.

When you ask to continue a task that previously used Seatbelt, the agent should load the skill even without a fresh invocation. If that state is lost, it should consult available history, report what remains unknown, and ask you to restore the settings and resolve uncertain boundaries before dependent execution. It should not assume a checkpoint passed or silently reset the protocol. Canceling a task retires its checkpoint while retaining the two settings.

## Noninteractive work and permissions

When a required checkpoint cannot be answered in a one-shot run, the agent should return the pending question and stop dependent work. It should accurately distinguish completed work from work still waiting.

Retain the full public output stream when continuing one-shot work. In the [recorded trials](trials/2026-11-04/README.md#known-limitations), both hosts included complete settings earlier in the stream but omitted the communication setting from the final message alone.

Comprehension and permission are separate. Passing a checkpoint grants no authority for deployment, deletion, spending, or other external actions. Seatbelt remains subject to the task's authorization and the host's higher-priority rules.

If a bundled reference cannot be read, the skill requires the next user-facing reply to identify the file and explain that core rules are being used. Claude followed that timing in two of three focused attempts and disclosed it late in the third. See the [recorded limitations](trials/2026-11-04/README.md#known-limitations).

These are model instructions, not runtime enforcement. See [behavioral testing](behavioral-testing.md) for expected scenarios and the recorded host/model coverage.
