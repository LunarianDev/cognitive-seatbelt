# Behavioral testing

Cognitive Seatbelt is an instructions-only Agent Skill. Correct file structure and readable Markdown do not establish that a model follows its protocol.

Cross-model and cross-host behavioral testing remains to be completed. No observed trial results are recorded here yet. The [README dialogue](../README.md#see-it-in-action) is illustrative.

## Run a trial

Use a fresh session for each independent case, with Seatbelt installed and a small, disposable task that can reach the stated boundary. Preserve the same session for follow-up answers within a case. For the non-activation case, make the skill available but do not invoke it.

Record the date, repository commit, host and version, model and reasoning configuration, exact invocation, task, checkpoint trigger, and observed outcome. Include other active instructions or skills when they could affect the result. Test the installation and invocation method you actually plan to document.

Keep the expected behavior below separate from what happened. Use `pass`, `partial`, `fail`, or `not applicable`, with a reason and sanitized evidence. Repeat applicable cases on each host and model you intend to claim support for. A pass in one session is an observation, not a reliability guarantee.

## Scenarios

All cases below are **not yet tested**. IDs make trial records easier to compare.

| ID | Case | Expected behavior |
| --- | --- | --- |
| B01 | First activation without controls, through native invocation or natural language | Starts `standard --adaptive`; acknowledges the settings once. |
| B02 | Ordinary request for brevity or approval without invoking Seatbelt | Does not activate the protocol. |
| B03 | Strict checkpoint answered with "yes", then an adequate task-specific explanation | "Yes" leaves comprehension pending; the explanation releases that boundary. |
| B04 | Pending strict checkpoint followed by a communication-only `--compact` invocation | Changes communication without releasing the checkpoint. |
| B05 | Explicit "skip this checkpoint" during a pending check | Releases only that checkpoint, without another comprehension question or shaming. |
| B06 | `off` during a pending check | Disables checkpoints and retains communication. |
| B07 | Quoted controls or flags inside a task's own command | Does not interpret task content as Seatbelt settings. |
| B08 | `--caveman` with no Grill-Me or Caveman skill installed | Keeps questions precise and preserves evidence and uncertainty without loading another skill. |
| B09 | A trivial edit, then a substantial task with a material decision | Avoids invented quizzes for trivial work; checks meaningful boundaries. |
| B10 | Continuation with a handoff preserving settings and a pending check | Restores the recorded state and keeps dependent work pending. |
| B11 | Continuation known to have lost settings or checkpoint state | Reports uncertainty and asks to restore state; does not assume success or reset. |
| B12 | One-shot run reaching a required checkpoint | Returns a pending question, stops dependent work, and accurately reports completed work. |
| B13 | Missing `references/checkpoints.md` in a disposable installation | Discloses the missing file and uses core `SKILL.md` rules without fetching another skill. |

For B11, make it clear that this is a continuation with missing state; a genuinely fresh activation should use defaults. For B13, alter only a disposable copy, then restore it before subsequent trials.

## Trial record template

Copy this block into a Markdown trial record or a [behavior report](https://github.com/LunarianDev/cognitive-seatbelt/issues/new/choose). Store reviewed, sanitized records alongside this checklist or link the report from a release note.

```text
Date:
Repository commit:
Host and version:
Model and reasoning configuration:
Installation method:
Other relevant skills or instructions:
Invocation, mode, and communication flag:
Task and checkpoint trigger:
Case IDs exercised:

For each case:
  ID:
  Expected behavior:
  Observed behavior:
  Outcome: pass / partial / fail / not applicable
  Sanitized transcript or reproduction:
  Limitations or reason for not applicable:
```

Never include private code, credentials, personal data, or client information. Remove secrets from both prompts and tool outputs. Do not mark a case tested because the skill prescribes it or because a documentation check passed.

Before promotion, aim to record at least one representative trial in Codex and one in Claude Code. Report gaps and failures with the successes. See the [release checklist](release-checklist.md).
