# Changelog

## 0.2.0 - 2026-11-25

*Scripted v0.2.0 trials are recorded in [docs/trials/2026-11-25/README.md](docs/trials/2026-11-25/README.md) across Codex CLI `0.171.0` (`gpt-6-astra / high`) and Claude Code `2.1.330` (`claude-fable-5-2`). No human walkthroughs have been recorded on v0.2.0 yet.*

### Added

- `--terse` as the primary terse-prose communication flag, keeping `--caveman` as a full alias.
- `--handoff` informational command (and natural-language trigger) to emit a compact, copy-pasteable state block for continuing in a new chat or worktree.
- Bundled companion reference [`skills/seatbelt/references/handoffs.md`](skills/seatbelt/references/handoffs.md) for `--handoff` templates, lost-state recovery, and one-shot resume format.
- Subtle first-checkpoint reply cue (`One sentence is enough. Reply "idk" for a hint, "explain", or "skip" to move on.`), shown once per session and after missed attempts.
- User-level calibration guidance (targeting blast radius, lock-in, and tradeoffs for experienced users; translating jargon and anchoring in concrete examples for learners) without adding a third required flag.
- Mentor pacing guardrails (no spoiled predictions, proportional teaching, user steering, debrief takeaway handoff).
- Windows install paths, a 10-second `--help` install check, an optional `npx skills add` note, a 4-row "Pick your mode" guide in the README, and an opt-in `AGENTS.md` / `CLAUDE.md` snippet in `docs/usage.md`.
- New v0.2.0 behavioral scenarios `B14`-`B18` in `docs/behavioral-testing.md` covering `mentor` pacing, senior calibration, multi-decision feature tasks, `--handoff`, and paused-turn UX hygiene.

### Changed

- **Checkpoint craft and tone:** Banned echo questions, banned hypothetical future trivia at handoffs, and forbade quoting `SKILL.md` rules or linking internal skill paths to the user.
- **Self-contained paused footer:** Replaced ad-hoc checkpoint explanations with one clean footer (`Checkpoint (<mode>, <--flag>) | Paused before: <boundary>` or `Execution complete; handoff pending`) in every paused turn's final message, addressing the v0.1.0 B12 final-message-only settings gap.
- **Single question per turn:** Instructed agents to finish planned preparation first and ask the checkpoint question once at the end of the turn.
- **Post-2-attempt flow:** Removed the wasted `"retry"` turn; after two missed attempts, the agent explains the missed point and accepts an immediate answer, lower mode, or `skip` in the next reply.
- **Mode-specific handoffs:** `standard` closes clean execution with a concise "what you now own" summary and pauses only on divergence or new risk; `strict` reuses covered answers and asks a final check only on skips, divergence, real caveats, or uncovered breaking changes; `mentor` closes with a short debrief.
- **Human interface and `off` behavior:** Surfaced `"idk"`, `"hint"`, `"explain"`, `"skip"`, and `"lower to <mode>"` across docs and `--help`. Changed plain `$seatbelt off` to disable checkpoints **and** reset prose to `--normal`; pair `off` with a flag (`off --terse`, `off --compact`) to keep terse prose without checkpoints.
- **README hero dialogue:** Replaced the v0.1.0 A/B/C echo example with an illustrative tradeoff and consequence checkpoint.
- **Shorter, scannable `SKILL.md`:** Broke up long prose walls into concise bullet rules and moved handoff/recovery serialization details into `references/handoffs.md`.

### Observed v0.2.0 trial status and known limitations

- **Verified improvements (both hosts):** 0 duplicate mid-turn questions, 0 wasted retry turns, and 100% self-contained two-control footers in final messages (`B12` fixed on both Codex and Claude); updated `off` / `off --terse` (`B06`), standalone `--terse` / `--caveman` (`B08`), `B13` missing-reference disclosure, `mentor` pacing (`B14`), senior calibration (`B15`), multi-decision `standard` handoff summaries (`B16`), and `--handoff` + fresh-session resume (`B17`) all passed functional checks. Claude Code (`2.1.330`, `claude-fable-5-2`) passed all 19 turns with 0 automated UX findings.
- **Known limitation (Codex policy citations):** `codex-cli 0.171.0` (`gpt-6-astra / high`) still intermittently added a `[Seatbelt](.../SKILL.md)` link and quoted rule line before the paused footer (2 of 3 spot-check turns on the final wording, down from 14/17 on the earlier candidate; `B12-final-footer` ran with 0 findings).
- **Known nuance (Claude `B15`):** When the user prompt supplied every implementation detail of `migrate(connection)` up front, Claude wrote `app/migration.py` and tests in Turn 1 while holding `data/users.sqlite3` and `app/users.py` at the unresolved caller boundary.
- **Coverage limits:** Scripted controller turns only (no v0.2.0 human walkthroughs yet); `B13` for a missing `references/handoffs.md` remains untested; Codex Cohort 1 hit 6 CLI stream timeouts under concurrency (0 timeouts in sequential retries and the spot check). See [docs/trials/2026-11-25/README.md](docs/trials/2026-11-25/README.md). The [v0.2.0 release](https://github.com/LunarianDev/cognitive-seatbelt/releases/tag/v0.2.0) includes the installable skill archive and checksum.

## 0.1.0 - 2026-11-04

### Added

- An illustrative task workflow and a shorter getting-started path.
- Separate guides for installation, controls, behavioral testing, and release preparation.
- Contribution guidance and GitHub issue and pull request templates.
- An MIT license in the repository and installable skill folder.
- Sanitized trial records for Codex CLI and Claude Code, including real human walkthroughs, revision hashes, and failed observations.

### Changed

- The README leads with the reason to use Seatbelt and explains participation separately from communication length.
- Continuing a Seatbelt-governed task restores missing state before dependent execution.
- Production edits implementing unresolved decisions wait while diagnostics, tests, and isolated drafts can proceed.
- Substantial handoffs explicitly reuse adequate earlier answers or ask about uncovered consequences. A skipped check supplies no understanding to reuse.
- Missing bundled references must be identified in the next user-facing reply, with the core-rule fallback explained.

### Known limitations

- Claude Code disclosed a missing reference late in one of three focused attempts, despite preserving the core checkpoint boundary.
- Complete pending settings appeared in the full one-shot output streams on both hosts; final-message-only output omitted the communication setting.

See the [trial report](docs/trials/2026-11-04/README.md) for exact configurations, revision coverage, and remaining gaps. These are small observed samples, not reliability or learning measurements. The [v0.1.0 release](https://github.com/LunarianDev/cognitive-seatbelt/releases/tag/v0.1.0) includes the installable skill archive and checksum.
