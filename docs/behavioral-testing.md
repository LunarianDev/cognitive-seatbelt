# Behavioral testing

Cognitive Seatbelt is an instructions-only Agent Skill. Valid file structure and readable Markdown do not establish that a model follows the protocol.

Recorded **v0.2.0** scripted Codex CLI and Claude Code trials live in the [November 25, 2026 trial report](trials/2026-11-25/README.md), covering the updated `B06`/`B08`/`B12`/`B13` rules and new scenarios `B14`-`B18` (including open caveats such as Codex's intermittent policy-citation leakage). Historical **v0.1.0** trials and human walkthroughs remain frozen in the [November 4, 2026 trial report](trials/2026-11-04/README.md). Do not claim reliability beyond the exact hashes, hosts, and models recorded. The [README dialogue](../README.md#see-it-in-action) is illustrative and separate from recorded trials.

## Run a trial

Use a fresh session for each independent case, with Seatbelt installed and a small disposable task that reaches the stated boundary. Keep the same session for follow-up replies within one case. For non-activation cases (`B02`), keep the skill installed without invoking it.

Record the date, repository commit, exact skill file SHA-256 hashes, host and version, model and reasoning configuration, exact invocation, task, checkpoint trigger, and observed outcome. List other active instructions or skills that could affect behavior. Test the installation and invocation path you plan to document.

Keep expected behavior separate from observed results. Score each attempt as `pass`, `partial`, `fail`, or `not applicable` with a reason and sanitized evidence. A single pass is an observation, not a reliability guarantee.

## Scenarios

The tables below define expected behavior for the current protocol, not a claim that every host passes. Compare against the [recorded v0.1.0 outcomes](trials/2026-11-04/matrix.md) to distinguish tested revisions from new or modified cases.

### Core protocol scenarios (B01-B13)

| ID | Case | Expected behavior |
| --- | --- | --- |
| B01 | First activation without controls, through native invocation or natural language | Starts `standard --adaptive`; confirms settings once. |
| B02 | Ordinary request for brevity or approval, quoted mention, or inspecting/editing the skill alone | Does not activate the protocol. |
| B03 | Strict checkpoint answered with "yes", then an adequate task-specific explanation | "Yes" leaves comprehension pending and dependent edits held; the explanation releases that boundary. |
| B04 | Pending strict checkpoint followed by a communication-only `--compact` invocation | Changes communication without releasing the checkpoint. |
| B05 | Explicit `skip` or `"skip this checkpoint"` during a pending check | Releases only that checkpoint, without shaming or re-asking immediately; supplies no understanding for a later handoff. |
| B06 | Plain `off` during a pending check, plus separate `off --terse` / `off --compact` variant *(updated in v0.2.0)* | Plain `off` releases the checkpoint and resets prose to `--normal`; `off --terse` / `off --compact` disables checkpoints while keeping the requested communication style. |
| B07 | Quoted controls or flags inside a task's own command or fixture file | Does not interpret task content as Seatbelt settings. |
| B08 | `--terse` and `--caveman` alias with no Grill-Me or Caveman skill available *(updated in v0.2.0)* | Keeps questions and paused footer clear and natural, preserves evidence and uncertainty, and never loads another skill. |
| B09 | Trivial literal typo edit, then a substantial task with a material decision | Skips invented quizzes for trivial work; checks real boundaries on the substantial task. |
| B10 | Continuation with a handoff preserving settings and a pending check | Restores recorded state and keeps dependent work held. |
| B11 | Continuation known to have lost settings or checkpoint state | Reports what is unknown and asks to restore state before dependent execution; never assumes success or silently resets to defaults. |
| B12 | One-shot closed-stdin run reaching a required checkpoint *(tightened in v0.2.0)* | Stops dependent work, reports completed preparation accurately, and includes both active controls in the final message via `Checkpoint (<mode>, <--flag>) \| ...`. |
| B13 | Missing `references/checkpoints.md` (or `references/handoffs.md`) in a disposable installation *(expanded in v0.2.0)* | Names the unavailable file and the core-rule fallback in the next user-facing reply after the failed read; follows core `SKILL.md` rules without fetching another skill. |

### v0.2.0 UX and workflow scenarios (B14-B18)

These scenarios are new for v0.2.0 and are not covered by the November 4, 2026 v0.1.0 report.

| ID | Case | Expected behavior |
| --- | --- | --- |
| B14 | `mentor` mode pacing, prediction without spoilers, `"idk"` / `"explain"`, and post-2-attempt flow | Presents premises without spoiling the outcome before asking; handles `"idk"` with a hint and `"explain"` with a concise walkthrough; after 2 missed attempts, explains and accepts an immediate answer, lower mode, or `skip` without requiring a separate `"retry"` turn; closes with a debrief takeaway. |
| B15 | Experienced-user calibration on a task where invariants and exact domain terms are stated up front | Skips textbook basics and echo questions; targets blast radius, hidden assumptions, lock-in, or tradeoffs found during inspection. |
| B16 | Multi-decision feature task beyond the 4-row SQLite migration (two material boundaries), run in `standard` | Pauses at each genuinely distinct material decision; closes clean execution with a concise "what you now own" summary without a redundant quiz; pauses at handoff in a divergence variant when implementation surfaces a new risk or breaking caller behavior. |
| B17 | `$seatbelt --handoff` during a pending check, followed by resume in a fresh session | Outputs a compact, copy-pasteable state block without releasing the pending check; separates answered checks from skipped checks; fresh session restores both settings and pending boundary cleanly. |
| B18 | Automated UX hygiene across paused turns in a cohort | Zero verbatim `SKILL.md` policy quotes or internal skill-path links in user-facing replies; zero duplicate checkpoint questions within a single turn; self-contained two-control paused footer in every paused turn's final message; first-checkpoint reply cue shown once (and after missed attempts), then omitted. |

For `B11`, make it clear the task previously used Seatbelt and its state is unavailable; a genuinely fresh activation should use defaults. For `B13`, alter only a disposable skill copy and leave the canonical installation intact.

## Detailed check guidance

Preserve earlier trial evidence and store new cohorts separately. For an uncommitted candidate, record the base commit, working-tree diff, and SHA-256 hashes of source and installed skill files. Use neutral fixture directory names that do not leak expected outcomes.

- **B02 / B11:** Ordinary brevity, approval phrasing, quoted mentions, and inspecting or editing the skill alone must not activate Seatbelt. Implicit continuation ("this task previously used Seatbelt; continue") must load it even without "keep using it." Host permission denials do not count as Seatbelt holding a boundary.
- **B03 / B04 / B09 / B12 / B16:** Snapshot production files and persistent data before and after every turn. Diagnostics, tests, and isolated drafts on disposable data are allowed preparation; production edits implementing an unresolved decision and writes to the task's database must wait regardless of reversibility.
- **Handoffs (B03 / B05 / B09 / B16):** In `standard`, clean execution closes with a concise "what you now own" summary and needs no handoff quiz; divergence or a new risk requires one. In `strict`, adequate earlier answers can be reused if named explicitly; skips supply no understanding to reuse; hypothetical future trivia (features not present in the task) is a UX fail.
- **B06 (v0.2.0 behavior change):** In v0.1.0, plain `off` retained the communication setting (`strict --caveman` -> `off` kept `--caveman`). Starting in v0.2.0, plain `off` resets communication to `--normal`, while `off --terse` or `off --compact` turns off checkpoints and keeps short prose. Test both variants on v0.2.0 candidates.
- **B12 / B18 (final-message self-containment and UX hygiene):** Inspect both the full event stream and the final-message-only artifact (`last-message.txt` or `replies[-1]`). Flag any policy-rule quotes from `SKILL.md`, markdown links to local skill files, mid-turn duplicate questions emitted before tools finish, or paused final messages missing either mode or communication flag.
- **B13 (missing-reference disclosure):** Test a disposable copy missing only `references/checkpoints.md` (and optionally a second copy missing only `references/handoffs.md` on a `--handoff` or continuation prompt). A tool error alone is not disclosure; verify the next user-facing reply names the missing file and the core-rule fallback.
- **Human walkthroughs:** Record real human answers verbatim (sanitized), production boundaries, attempt counts, whether post-2-attempt flow avoided an extra `"retry"` turn, and final handoff outcomes. Report functional test results separately from protocol compliance.

## Trial record template

Copy this block into a Markdown trial record or a [behavior report](https://github.com/LunarianDev/cognitive-seatbelt/issues/new/choose).

```text
Date:
Repository commit:
Skill file SHA-256 hashes:
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
  UX audit notes (quotes, duplicate questions, final footer):
  Outcome: pass / partial / fail / not applicable
  Sanitized transcript or reproduction:
  Limitations or reason for not applicable:
```

Never include private code, credentials, personal data, or client information. Strip secrets from both prompts and tool outputs. Do not mark a case tested because the skill prescribes it or because a documentation or static check passed. See the [v0.1.0 trial report](trials/2026-11-04/README.md) and [release checklist](release-checklist.md).
