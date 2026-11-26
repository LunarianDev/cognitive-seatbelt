# Recorded v0.2.0 trials, November 25, 2026

These local Codex CLI and Claude Code trials exercise the v0.2.0 UX overhaul merged into `main` in [PR #2](https://github.com/LunarianDev/cognitive-seatbelt/pull/2) (built off `da6646a`). They test the updated `off` reset rule, `--terse` and its `--caveman` alias, self-contained final-message footers (`B12`), missing-reference disclosure (`B13`), `mentor` pacing and no-wasted-retry flow (`B14`), experienced-user calibration (`B15`), multi-decision webhook tasks and `standard` handoff summaries (`B16`), `--handoff` plus fresh-session resume (`B17`), and automated UX hygiene (`B18`).

All follow-up replies in these trials were scripted by controller harnesses; no human walkthroughs have been recorded on v0.2.0 yet. These are small observed samples, not general reliability guarantees or proof of learning gains. Publication status and packaged files are recorded on the [v0.2.0 release page](https://github.com/LunarianDev/cognitive-seatbelt/releases/tag/v0.2.0). Historical v0.1.0 evidence remains frozen under [docs/trials/2026-11-04/](../2026-11-04/README.md).

## Tested configurations

| Host | Version | Model | Effort | Invocation |
| --- | --- | --- | --- | --- |
| Codex CLI | `0.171.0` | `gpt-6-astra` | `high` | Native `$seatbelt`, closed-stdin `exec --json` with exact-session resume |
| Claude Code | `2.1.330` | `claude-fable-5-2` | host default | Native `/seatbelt`, `claude -p --output-format stream-json` with `--session-id` and `--resume` |

Complete personal installations matched the repository source byte for byte (`~/.agents/skills/seatbelt`, with `~/.claude/skills/seatbelt` linked to it). Isolated `B08` and `B13` runs used per-run isolation without altering the shared personal installation: Codex enforced process-local filesystem `deny` rules plus `skills.config` disables for `caveman*` and `grill*`; Claude used `--setting-sources project,local` with a project-local `.claude/skills/seatbelt/` copy.

## Revision identity and hashes

Two closely related v0.2.0 candidate hashes were exercised:

| Candidate | `SKILL.md` SHA-256 | `references/checkpoints.md` SHA-256 | `references/handoffs.md` SHA-256 | Coverage |
| --- | --- | --- | --- | --- |
| `v0.2.0-rc1` (Cohort 1) | `2dc0f1aa64fc12bda63def2238dd9fea4c382dc4b9f25fd714a8dd2af08b91a2` | `d5604c1df4419e13a992da7a1a3492e06e9cb9b4434691cd4122501b4c7f8336` | `182019279038ea478dfb7319da5ed313d8a38b180eecc4106dd9515ea735ab88` | Codex full v0.2.0 cohort (`v020-20261124-232600`) |
| `v0.2.0-rc2` (Final branch) | `1d0b0f0adf3035bf7641702835728e4996f247626a9d7099cd2d5d803a616d6d` | `11c3a0642cc0f3108c3711443ab601ce83a693b0231acdfb89d4444a6c0f430f` | `182019279038ea478dfb7319da5ed313d8a38b180eecc4106dd9515ea735ab88` | Codex 3-case spot check (`v020-20261125-012437`) + full Claude cohort (`v020-claude-20261125-034227`) |

Shared unchanged files across both candidates:
- `LICENSE`: `33dac935481fb6abe708f0a35b50144863d102cf98abec52b5e4849b7904e814`
- `agents/openai.yaml`: `6f69147b1d2a8292b2c189973e4aa88dc9fe082cbfe8090da86c3a76597b7deb` (LF-normalized Git blob: `83281e63fe0971db19e70f5c4f99ca3a1497d5969ec571f741268628e2bcb082`)

The single difference between `rc1` and `rc2` moves the no-policy-citation prohibition directly into `Run a checkpoint` step 2 of `SKILL.md` with explicit bad-pattern examples.

## Observed results

Full per-case outcomes and session IDs are in [matrix.md](matrix.md).

### Automated UX audit (`B18`) comparison

| UX check | v0.1.0 Codex (`matrix-20261104-012513`, 26 paused) | v0.2.0 Codex `rc1` (`v020-20261124-232600`, 17 paused) | v0.2.0 Codex `rc2` spot check (`v020-20261125-012437`, 3 paused) | v0.2.0 Claude `rc2` (`v020-claude-20261125-034227`, 12 paused) |
| --- | --- | --- | --- | --- |
| `duplicate_mid_turn_question` | 3 turns | **0** | **0** | **0** |
| `missing_final_settings` | 22 / 26 paused | **0 / 17** | **0 / 3** | **0 / 12** |
| `missing_v020_footer` | n/a | **0 / 17** | **0 / 3** | **0 / 12** |
| `wasted_retry_prompt` | 1 (v0.1.0 Claude human) | **0** | **0** | **0** |
| `skill_path_link` | 23 turns | 14 / 17 paused | 2 / 3 paused | **0 / 12** |
| `policy_quote_leakage` | 23 turns | 13 / 17 paused | 2 / 3 paused | **0 / 12** |

### Functional and protocol highlights

- **Self-contained `B12` final messages:** Both Codex (`last-message.txt`) and Claude (`result_text`) ended every paused turn with `Checkpoint (<mode>, <--flag>) | ...`, closing the v0.1.0 final-message-only settings gap on both hosts.
- **Updated `B06` `off` behavior:** On both hosts, plain `off` from `strict --terse` released the checkpoint and restored normal prose, while `off --terse` from `strict --normal` released the checkpoint and used terse prose. All four completed migration fixtures passed subject unit tests and all 5 controller acceptance checks.
- **Standalone `--terse` and `--caveman` alias (`B08`):** Both hosts resolved `--caveman` cleanly to `--terse` in the paused footer with companion skills isolated (`9` active filesystem `deny` entries and `0` companion catalog entries on Codex; `0` companion entries in `init.skills` on Claude).
- **Missing-reference disclosure (`B13`):** Both hosts named `references/checkpoints.md` as missing and noted the core-rule fallback in the next user-facing reply before pausing.
- **Mentor pacing and no-wasted-retry flow (`B14`):** Both hosts withheld spoiler test results before the Turn 1 prediction, gave a concrete non-shaming hint on `"idk"`, explained clearly after two wrong attempts and invited an immediate answer (no `"retry"` menu), accepted the immediate answer, implemented `app/worker.py` with passing tests (`14` tests on Codex, `13` tests on Claude), and closed with a concise mentor debrief takeaway.
- **Experienced-user calibration (`B15`):** Both hosts skipped basic `NOT NULL` and savepoint trivia when invariants were stated up front, spotted the secondary index `idx_users_email` and the legacy caller `app/users.py:create_user(connection, email=None)`, and aimed the checkpoint at caller compatibility.
- **Multi-decision feature task (`B16`):** On the webhook delivery fixture with the HTTP 429 contract note, both hosts paused on the genuine at-least-once vs. at-most-once delivery tradeoff, implemented `app/worker.py` with passing tests (`17` tests on Codex, `18` tests on Claude), and closed clean `standard`-mode execution with a concise "what you now own" summary rather than a redundant handoff quiz.
- **`--handoff` and fresh-session resume (`B17`):** On both hosts, `--handoff` emitted the structured copy-pasteable block from `references/handoffs.md` without releasing the pending checkpoint. A brand-new session in a relocated fixture copy restored `strict --compact` and kept the waiting boundary held.

## Known limitations and open caveats

1. **Codex policy-citation leakage (host-specific):** Even on `rc2` (`1d0b0f0a...`), `codex-cli 0.171.0` (`gpt-6-astra / high`) still emitted a `[Seatbelt](.../SKILL.md)` link and a quoted rule line before the clean footer in 2 of 3 spot-check turns (`B01-native`, `B15-senior-calibration`), while `B12-final-footer` ran with 0 findings. Claude Code (`2.1.330`, `claude-fable-5-2`) emitted 0 policy quotes and 0 skill-path links across all 19 turns.
2. **Codex CLI `0.171.0` stream timeouts:** In Codex Cohort 1 (`v020-20261124-232600`), 6 individual turns timed out at the stream boundary after `turn.started`, mostly under 4-way parallel runs (`B15`, `B16-clean`, `B17`, `B17-retry` Turn 1; `B06-off-reset` Turn 2; `B14-mentor` Turn 2; `B14-mentor-retry` Turn 3). Sequential retries and the Cohort 2 spot check ran with 0 timeouts. Claude Code ran all 19 turns with 0 timeouts.
3. **Claude `B15-senior-calibration` production-file timing nuance:** Because the senior prompt supplied every implementation detail of `migrate(connection)` explicitly (savepoint rebuild, `COALESCE`, explicit `id`, `PRAGMA table_info` guard), Claude wrote `app/migration.py` and `tests/test_migration.py` (including index recreation) in Turn 1 while holding `data/users.sqlite3` and `app/users.py` for the unresolved caller decision. Codex kept `app/migration.py` in `.migration-draft/` until the caller boundary resolved.
4. **Coverage scope:** All v0.2.0 runs used scripted controller inputs rather than live human walkthroughs. Cases `B02`, `B03`, `B04`, `B05`, `B07`, `B09`, and `B11` were covered in the v0.1.0 report and not re-run in full here; `B13` for a missing `references/handoffs.md` file remains untested. Raw session logs remain in the local trial workspaces (`seatbelt-codex`, `seatbelt-claude`).
