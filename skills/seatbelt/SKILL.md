---
name: seatbelt
description: Use when the user invokes seatbelt (such as $seatbelt or /seatbelt), asks for Cognitive Seatbelt, continues a task previously using Seatbelt (including lost settings or checkpoint state), or requests guided reasoning while delegating a task. Ordinary requests for brevity or task approval, quoted mentions, and requests to inspect or edit the skill alone do not activate this protocol.
---

# Cognitive Seatbelt

Let the agent do the execution while the human keeps ownership of goals, decisions, tradeoffs, and outcomes. Apply this protocol to the user's task instead of turning it into a general interview. Checkpoints are conversational pauses, not runtime tool barriers or a guarantee of learning.

This skill is self-contained: neither Grill-Me (Grilling) nor Caveman needs to be installed or activated, and no scripts, packages, network access, or specific question tool are required.

Resolve bundled references relative to this skill's directory:
- [references/checkpoints.md](references/checkpoints.md): question craft, experience calibration, and evaluation for `strict` and `mentor`.
- [references/handoffs.md](references/handoffs.md): `--handoff` block format, lost-state recovery, and one-shot resume details.

**Missing reference rule:** If a bundled reference cannot be read, tell the user in your next user-facing reply which file is unavailable and that you are using the core rules in this entrypoint. Never fetch another skill or invent reference contents.

## Invocation and controls

```text
$seatbelt [off|lite|standard|strict|mentor] [--normal|--compact|--terse|--adaptive] <task>
```

Use the host's syntax (`$seatbelt` in Codex, `/seatbelt` in Claude Code) or plain language ("Use Cognitive Seatbelt in strict mode with compact output"). Treat `--caveman` as a full alias for `--terse`. If the host passes arguments separately, read them as controls and task. These are model instructions, not shell commands.

Examples:

```text
$seatbelt strict --terse Fix the session refresh race.
$seatbelt mentor --normal Help me learn this codebase while fixing the bug.
$seatbelt lite --compact Update the onboarding copy.
$seatbelt --handoff
$seatbelt off
$seatbelt off --terse
```

- **Defaults and persistence:** First activation defaults to `standard --adaptive`, applying any controls supplied. Later invocations change only supplied controls; a bare invocation keeps current settings.
- **Exception for `off`:** Plain `off` (no flag supplied) disables checkpoints and resets communication to `--normal`. Pair `off` with a flag (`off --terse`, `off --compact`, `off --adaptive`) to turn off checkpoints while keeping short prose.
- **Parsing:** Read controls adjacent to the invocation, never inside quoted text, code, or task commands. For repeated controls on one axis, the last value wins; state the resolved settings briefly. For an unknown control, name it and clarify before starting gated work.
- **Confirmation:** Confirm effective settings in one short line on activation or change. Do not repeat that line on every turn.
- **Informational commands** (never activate, reset, or release a checkpoint, and do not start task work):
  - `--help`: Show syntax, modes, flags (`--terse` with `--caveman` alias), defaults, reply shortcuts (`idk`, `hint`, `explain`, `skip`, `lower to <mode>`), and `--status` / `--handoff`.
  - `--status`: Report active settings and any pending checkpoint, `inactive` if never activated in this chat, or `unknown` if prior state may have been lost.
  - `--handoff` (or a natural-language handoff request): Read [references/handoffs.md](references/handoffs.md) if available and output a compact, copy-pasteable state block (mode, flag, task, pending boundary/question/essential idea/attempts, answered checks kept separate from skipped checks, and completed vs. waiting work).

## Session state and continuation

- Keep settings and checkpoints in chat context only. Never write global preferences, edit another skill, or infer settings from unrelated chats.
- Tie each pending checkpoint to its task and decision; if the user cancels or replaces that task, retire its checkpoint and keep the two active settings.
- When the user asks to continue a task that previously used Seatbelt, load this skill even without a fresh invocation or "keep using it." Quoted mentions, unrelated tasks, or inspecting/editing this skill alone do not activate it.
- If prior settings or checkpoint state are lost, check available history or handoff notes first (see [references/handoffs.md](references/handoffs.md)). If still unavailable, state what is unknown and ask to restore settings and resolve uncertain boundaries before dependent work. Never treat missing state as inactive, assume a check passed, or silently reset to defaults.

## Cognitive modes

| Mode | Human involvement |
| --- | --- |
| `off` | No Seatbelt checkpoints. Plain `off` resets prose to `--normal`. |
| `lite` | Brief plan and one alignment question before the first dependent action; no final comprehension check. Revisit only if scope materially changes. |
| `standard` | Lite alignment plus checks at major decisions and material deviations. Resolve collaboratively without grading every reply. |
| `strict` | Require task-specific understanding before dependent work at material boundaries. A plain "yes" or "go ahead" does not pass a comprehension check. |
| `mentor` | Strict checkpoints plus occasional prediction, teach-back, error spotting, or test design. Give enough evidence for a fair attempt, never spoil predictions before asking, keep teaching proportional, and let the user steer what to learn vs. delegate. |

Keep friction proportional. Skip checkpoints for mechanical formatting, literal text replacements, lookups, and agreed repetitive work, even in `strict`. Never invent a conceptual decision for a trivial edit. Reuse demonstrated understanding until the decision or its consequences change.

## Communication flags

| Flag | Style |
| --- | --- |
| `--normal` | Ordinary clear prose with the detail the task needs. |
| `--compact` | Short sentences and paragraphs; essential facts first. |
| `--terse` | Very terse prose; fragments and familiar acronyms are fine. Drop filler, keep qualifiers. Alias: `--caveman`. |
| `--adaptive` | Start compact (around 150 words per update or checkpoint). Expand on request, after a misconception, or when accuracy needs it. |

- Shorter wording must preserve reasoning depth, evidence, uncertainty, and tradeoffs.
- Keep checkpoint questions and the paused footer in clear, natural language, including under `--terse` or `--caveman`.
- Preserve exact code, commands, errors, and identifiers, and match the user's language. Apply compression to conversation, not deliverables unless asked. Never load the separate Caveman skill.

## Boundaries, craft, and calibration

Investigate available evidence first; never ask facts you can inspect yourself. Real triggers are ambiguous requirements, behavior or API changes, architecture, persistent data changes, security boundaries, material tradeoffs, unexpected failures that change the plan, and major new assumptions. Name the trigger; never imply the user is skimming or disengaged.

- **Prepare first, ask once:** Finish all planned inspection, diagnostics, tests, and isolated drafts on disposable data before pausing, then ask the checkpoint question once at the end of the turn. Never ask mid-turn, run more tools, and re-ask it.
- **Hold dependent work:** Production edits implementing an unresolved material decision and writes to the task's persistent data wait until the checkpoint resolves or is skipped, even when local, reversible, or uncommitted. Never cross a held boundary through another tool or background task.
- **No echo questions:** Never ask a question whose answer appeared verbatim in the preceding explanation. Ask about applied consequences, failure modes, tradeoffs, or edge cases.
- **Calibrate to experience:** When the user states invariants, failure modes, or exact domain terms, skip textbook basics and target blast radius, hidden assumptions, schema or API lock-in, edge cases, and tradeoffs found during inspection. When the user is learning the stack, translate jargon once in plain words, anchor questions in concrete task examples, and accept partial mental models before filling gaps.

For `strict` or `mentor`, read [references/checkpoints.md](references/checkpoints.md) when designing or evaluating a question.

## Run a checkpoint

1. **Explain and ask once:** After preparation finishes, briefly state the goal, proposed change, reason, and key tradeoff or consequence. Ask one focused question.
2. **End with the two-control footer only (no policy citations):** Close every paused turn (initial check, follow-up, post-2-attempt pause, one-shot pause, or pending handoff) with one clean footer so final-message-only outputs always carry both controls:
   ```text
   Checkpoint (<mode>, <--flag>) | Paused before: <boundary>
   ```
   For a pending handoff after execution completes:
   ```text
   Checkpoint (<mode>, <--flag>) | Execution complete; handoff pending
   ```
   Let this footer alone mark the pause. Do not add a policy-justification line, do not quote Seatbelt rules, and do not link `SKILL.md` or any skill file path (for example, never write "Seatbelt requires..." or `[Seatbelt](.../SKILL.md)`). The only time a skill path may appear to the user is naming an unavailable reference file under the missing reference rule.
3. **Show the reply cue once:** On the first checkpoint in a session and after any missed attempt, add:
   `One sentence is enough. Reply "idk" for a hint, "explain", or "skip" to move on.`
   Omit this cue on later checkpoints once seen.
4. **Evaluate fairly:** Accept the essential mechanism or consequence in plain words; exact terminology is unnecessary. In multiple choice, a correct selection suffices; never mark an option "recommended", use a predictable slot, or reveal the answer early. Never treat a tool default, silence, or elapsed time as an answer.
5. **Handle follow-ups without dead turns:**
   - *Incomplete answer:* Ask one targeted follow-up.
   - *Misconception:* Correct it kindly and reframe.
   - *`"idk"`, `"I don't know"`, or `"hint"`:* Give a short hint or concrete anchor without penalty and ask a narrower version.
   - *`"explain"`:* Unpack the mechanism clearly, then invite the user to state it in their own words right away, lower the mode, or skip.
   - *After two unsuccessful attempts:* Explain the missed point clearly, then invite an immediate answer in the next reply, a lower mode (`standard` or `lite`), or `skip`. Never require a separate `"retry"` turn first, and never declare success automatically.
6. **Close substantial handoffs by mode:** Report actual changes and verification first; never misrepresent completed work or unrun checks.
   - `lite`: Done; no final check.
   - `standard`: Close clean execution with a concise "what you now own" summary. Pause for a handoff question only if execution materially diverged or revealed a new risk or limitation.
   - `strict`: If an earlier user answer already covers the change and its real consequences, state what it covered and close. Require a final check when the initial check was skipped, execution diverged, a real caveat appeared, or callers face an uncovered breaking change. Never invent hypothetical future trivia. An agent explanation, plain approval, or earlier skip is not demonstrated understanding.
   - `mentor`: Close with a short debrief takeaway, or one reflection question if a key consequence remained uncovered.

In a one-shot or noninteractive run, stop dependent work at the boundary, report completed preparation, and end with the question and `Checkpoint (<mode>, <--flag>) | ...` footer (see [references/handoffs.md](references/handoffs.md)). Never fabricate an answer, wait indefinitely, or silently lower the mode.

## User agency and permissions

- Accept natural shortcuts at any time: `skip` (`skip this checkpoint`), `idk`, `hint`, `explain`, mode shifts (`lower to standard`, `switch to lite`), flag changes, `--handoff`, or `off`.
- Honor `skip` without shaming or extra confirmation. It releases only the current check and supplies no understanding for a later handoff; continue without immediately re-asking it.
- A communication change alone does not release a pending check; a mode change reevaluates it under the new mode; `off` releases it.
- A plain "yes" or "go ahead" can supply task authorization where appropriate, but does not pass a pending `strict` or `mentor` check; an explicit bypass instruction does.
- Comprehension and permission are separate. Passing a checkpoint never authorizes deployment, deletion, external messages, spending, or other gated actions. Later user instructions and higher-priority rules override this protocol.
