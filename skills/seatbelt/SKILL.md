---
name: seatbelt
description: Preserve human understanding during agent work when the user invokes $seatbelt, asks for Cognitive Seatbelt, or requests comprehension checkpoints or guided reasoning while delegating a task. Supports independent cognitive modes and communication flags. Ordinary requests for brevity or task approval alone do not activate this protocol.
---

# Cognitive Seatbelt

Let the agent absorb execution effort while the human retains understanding of goals, decisions, tradeoffs, and outcomes. Add this protocol to the user's actual task; do not replace that task with a general interview. This is an instructions-only skill: checkpoints are conversational behavior, not runtime tool barriers or a guarantee of learning.

## Invocation and two independent controls

```text
$seatbelt [off|lite|standard|strict|mentor] [--normal|--compact|--caveman|--adaptive] <task>
```

Examples:

```text
$seatbelt strict --caveman Fix the session refresh race.
$seatbelt mentor --compact Help me learn this codebase while fixing the bug.
$seatbelt lite --normal Update the onboarding text.
$seatbelt --compact
$seatbelt off
```

First activation defaults to `standard --adaptive`. Later invocations change only explicitly supplied settings; bare `$seatbelt` retains the active settings. Switching cognitive mode never changes communication style, and changing a communication flag never changes cognitive mode. `off` disables Seatbelt questions while retaining the communication setting; `off --normal` returns both controls to ordinary behavior.

Read controls adjacent to the invocation, not flags appearing inside quoted material, code, or the task's own commands. For repeated settings on one axis, the last supplied value wins; briefly state the resolved settings. If a control is unknown, identify it and clarify rather than invent a mode or start gated execution under an assumed setting.

`$seatbelt --help` shows syntax, modes, flags, and defaults. `$seatbelt --status` reports the current settings and any pending checkpoint, or reports inactive if it has never been activated in this chat. These are informational commands: they do not activate, reset, or release a checkpoint, and need not start task work.

Keep settings and pending checkpoints in this chat's context, including a handoff summary when available. Associate each checkpoint with its task and decision; retire it if the user cancels or replaces that task, while retaining the two settings. Do not write global preferences, alter another skill, or infer settings from unrelated chats. On activation or a change, acknowledge the effective mode and communication setting in one short line. Do not repeat the banner on every response.

## Cognitive modes

| Mode | When and how to involve the human |
|---|---|
| `off` | No Seatbelt checkpoints. Continue ordinary task work. |
| `lite` | For meaningful work, give a brief plan and ask one alignment question before the first dependent action. After the user answers, execute without recurring quizzes; revisit alignment only if the goal or scope materially changes. |
| `standard` | Use Lite alignment, then pause at major decisions, material deviations, and the handoff of substantial work. Ask for the user's choice, interpretation, or a short explanation. Resolve ambiguity collaboratively; do not grade every answer. |
| `strict` | At those boundaries, require a task-specific demonstration of understanding before the dependent action. An approval such as "yes" is insufficient for a comprehension question. Use the checkpoint procedure below. |
| `mentor` | Use Strict checkpoints and occasional prediction, teach-back, error spotting, or a proposed test. Let the user attempt the reasoning before revealing the explanation, with enough evidence to make a fair attempt. Then perform the execution. |

Use judgment to keep friction proportional to the work. Mechanical formatting, literal text replacements, repository lookup, and already-agreed repetitive execution normally need no checkpoint, even in Strict. Do not manufacture a conceptual decision for a trivial task. A small meaningful task usually needs one combined checkpoint; a substantial task needs several at real boundaries, not one per tool call. Reuse demonstrated understanding until the decision or its consequences change.

## Communication flags

| Flag | Output behavior |
|---|---|
| `--normal` | Ordinary clear prose, with the detail the task needs. |
| `--compact` | Short sentences and paragraphs; essential facts first. |
| `--caveman` | Very terse prose; fragments and familiar acronyms are acceptable. Remove filler, never necessary qualifiers. |
| `--adaptive` | Start compact, generally within about 150 words per update or checkpoint. Expand when requested, when an answer reveals a misconception, or when accuracy requires it. This is a target, not a hard limit. |

Compression reduces wording, not reasoning depth, evidence quality, uncertainty, or relevant tradeoffs. Preserve what changes, why, meaningful assumptions, consequences, and the user's decision. Use progressive disclosure: essential facts first, then a targeted explanation if needed. Offer supporting rationale and evidence, not private internal deliberation.

Questions always use natural, precise language, including in Caveman mode. Expand ambiguous compressed wording immediately. Preserve exact code, commands, errors, and technical names. Match the user's language. Apply compression to conversational explanations, not the content of code, documents, PR descriptions, or other deliverables unless requested. Implement this style here without depending on or activating the separate Caveman skill.

## Choose meaningful boundaries

Investigate available evidence first. Ask about understanding and judgment, not facts the agent can retrieve. Useful triggers include requirements interpretation, behavior or API changes, architecture, persistent data changes, security boundaries, material tradeoffs, unexpected failures that change the approach, and significant new assumptions. State the actual trigger; never infer that the user is skimming, disengaged, or cognitively impaired.

Finish enough discovery and preparation to make the decision concrete and reviewable before pausing. Existing user answers can satisfy alignment when they already demonstrate the relevant understanding. Do not ask the same question again because the skill was reinvoked.

## Run a checkpoint

1. Briefly explain the concrete goal, proposed change, relevant reason, and material consequence or tradeoff. Ask one focused question. For Strict or Mentor, read [references/checkpoints.md](references/checkpoints.md) when selecting or evaluating a question.
2. Name the boundary that is waiting and briefly explain that the selected Seatbelt mode calls for this check. Hold the action that depends on the answer. Independent discovery, diagnostics, or reversible preparation may continue, but do not cross the boundary through a different tool or background task. If needed, yield with the question pending.
3. In Strict or Mentor, accept an answer that captures the essential mechanism or consequence, in the user's own words; exact vocabulary is unnecessary. In multiple choice, a correct selection suffices when recognition is the chosen check. Confirm briefly and continue. For a consequential decision or uncertain recognition, choose teach-back instead of moving the goalposts after a correct answer.
4. For an incomplete answer, ask one targeted follow-up. For a misconception, explain it kindly and reframe the question. "I don't know" prompts a short explanation or hint, not a penalty. After two unsuccessful attempts at the same checkpoint, offer explanation plus retry, a lower mode, or skipping this checkpoint. Do not retry endlessly or automatically declare success.
5. At the handoff of substantial work, report the actual result and validation first. Standard can request a brief summary; Strict or Mentor can ask for one sentence capturing what changed and a relevant consequence. If this final checkpoint is pending, state that execution is finished and the handoff check remains; never misrepresent completed work or unrun checks.

Use a supported question tool when suitable, otherwise ask in chat and yield. Never treat a tool's preselected option, silence, or elapsed time as an answer. For comprehension quizzes, avoid answer options marked "recommended" or any default that reveals the answer; use a free-text tool question or plain chat options if necessary.

## User agency and permissions

The user may change modes or flags at any time, say "skip this checkpoint", or turn Seatbelt off. Honor an explicit skip without shaming or another confirmation; it skips only the current Seatbelt check. A communication change alone does not release a pending check. A mode change reevaluates it under the new mode; Off releases it. A plain "go ahead" supplies authorization where appropriate but does not silently pass an active Strict comprehension check. A later explicit instruction to bypass the check does.

Comprehension and permission are separate. Passing a question does not authorize deployment, deletion, messages, spending, or another external action; use the task's existing authorization rules. Conversely, selecting Strict explicitly requests these conversational pauses. Do not introduce Seatbelt quizzes merely because normal task approval is needed. Later user instructions and higher-priority constraints take precedence over this protocol.
