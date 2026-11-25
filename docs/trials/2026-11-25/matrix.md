# v0.2.0 trial matrix, November 25, 2026

Outcomes for scripted Codex CLI (`0.171.0`, `gpt-6-astra / high`) and Claude Code (`2.1.330`, `claude-fable-5-2`) trials on `dev/v0.2.0`. See [README.md](README.md) for exact skill hashes, setup, and limitations.

## Codex CLI `rc1` cohort (`v020-20261124-232600`)

Skill hashes: `SKILL.md` `2dc0f1aa...`, `references/checkpoints.md` `d5604c1d...`, `references/handoffs.md` `18201927...`.

| Case | Turns | Session ID | Functional | UX findings | Outcome | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| `smoke` (`--help`) | 1 | `01a21198-e2fd-7b93-b673-72902a0d5f1a` | n/a | 0 | Pass | Showed `--terse`/`--caveman`, updated `off`, `--handoff`, reply shortcuts; stayed inactive. |
| `B01-native` | 1 | `01a21199-48b8-7eb0-bbf8-0678362d28c5` | n/a (held) | 2 (`link`, `quote`) | Partial (UX) | Protocol/boundary/footer passed (`standard --adaptive`); leaked skill link + policy quote. |
| `B06-off-reset` | 2 | `01a21199-48b0-79c0-9817-ce312a84fa0b` | n/a | 2 (Turn 1) | Blocked (Turn 2 timeout) | Turn 1 paused cleanly (`strict --terse`); Turn 2 stalled after `turn.started` (CLI timeout). |
| `B06-off-reset-retry` | 2 | `01a211af-6c38-7103-974a-71419763ba58` | 9 unit + 5 acceptance pass | 2 (Turn 1), 0 (Turn 2) | Pass (protocol) / Partial (UX) | Plain `$seatbelt off` released checkpoint and restored normal prose. |
| `B06-off-terse` | 2 | `01a21199-48c2-7903-843f-55824df44d35` | 10 unit + 5 acceptance pass | 1 (Turn 1), 0 (Turn 2) | Pass (protocol) / Partial (UX) | `$seatbelt off --terse` released checkpoint and switched to terse prose. |
| `B08-terse` | 1 | `01a21199-48c9-7c03-b54c-f23b3ffa1572` | n/a (held) | 2 (`link`, `quote`) | Pass (isolation) / Partial (UX) | 9 filesystem `deny` rules, 0 companion catalog entries; held production files. |
| `B08-caveman-alias` | 1 | `01a2119a-923c-7550-bb93-3b81f0222849` | n/a (held) | 2 (`link`, `quote`) | Pass (isolation) / Partial (UX) | 9 filesystem `deny` rules, 0 companions; `--caveman` resolved to `--terse` in footer. |
| `B12-final-footer` | 1 | `01a2119a-a713-7732-834b-0dbc494e9eb7` | n/a (held) | 2 (`link`, `quote`) | Pass (B12) / Partial (UX) | `last-message.txt` included `Checkpoint (strict, --normal) \| ...`; production files held. |
| `B13-missing-checkpoints` | 1 | `01a2119a-e08f-7930-8789-8a221fda1a01` | n/a (held) | 1 (`link`) | Pass (B13) / Partial (UX) | Disclosed missing `references/checkpoints.md` in reply 1; held production files. |
| `B14-mentor` | 2 | `01a2119a-e980-7aa0-8a3d-ee7102576a5c` | n/a | 0 | Blocked (Turn 2 timeout) | Turn 1 clean (0 findings, non-spoiler prediction); Turn 2 stalled on CLI resume. |
| `B14-mentor-retry` | 7 | `01a211b0-d588-71c2-aa1c-5e801a3d9e05` | 14 unit pass | 0 (T1-T2, T7), 2 each (T4-T6) | Pass (protocol) / Partial (UX) | Turn 3 CLI timeout; T1-T2 (`idk` hint), T4-T5 (2 misses -> immediate answer, no `"retry"` turn), T6 (cue omitted on 2nd checkpoint), T7 (mentor debrief + 14 passing tests). |
| `B15-senior-calibration` | 1 | `01a2119b-9721-7152-893a-e7b20c6a1e35` | n/a | 0 | Blocked (CLI timeout) | Timed out at 480s under 4-way batch. |
| `B15-senior-calibration-retry` | 2 | `01a211b1-8e6d-7bf3-a54f-48736f7cc6d0` | n/a (held) | 2 (T1), 0 (T2 `--handoff`) | Pass (calibration + handoff) / Partial (UX) | Skipped basics, targeted `idx_users_email` and `create_user(connection, email=None)`; T2 `--handoff` produced exact structured state block with checkpoint held. |
| `B16-multi-decision-clean` | 1 | `01a2119b-9f37-7ac0-b0a1-8ebac665c953` | n/a | 0 | Blocked (CLI timeout) | Timed out at 480s under 4-way batch. |
| `B16-multi-decision-divergence` | 2 | `01a2119c-0678-7480-8882-7311c6beba69` | 17 unit pass | 1 (T1), 0 (T2) | Pass (protocol) / Partial (UX) | Caught 429 contract note and delivery tradeoff; T2 implemented and closed `standard` cleanly with a concise ownership summary. |
| `B17-handoff` / `B17-handoff-retry` | 1 each | `01a2119c-2b56-7332-be18-b98c7983d86e`, `01a211b3-1fd6-7ee1-adcd-76b5c902e5f7` | n/a | 0 | Blocked (CLI timeout) | Stalled mid-turn on CLI; `--handoff` covered cleanly via `B15-senior-calibration-retry` Turn 2 + `B17-resumed`. |
| `B17-resumed` | 1 | `01a211b8-6002-77c2-ae06-7e0016e31960` | n/a (held) | 2 (`link`, `quote`) | Pass (resume) / Partial (UX) | Fresh session in relocated copy restored `strict --compact` from Turn 2 `--handoff` and held production files. |

## Codex CLI `rc2` spot check (`v020-20261125-012437`)

Skill hashes: `SKILL.md` `1d0b0f0a...`, `references/checkpoints.md` `11c3a064...`, `references/handoffs.md` `18201927...`. Sequential (`--workers 1`), 0 timeouts.

| Case | Turns | Session ID | Production held | UX findings | Outcome | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| `smoke` (`--help`) | 1 | `01a21205-7c78-7893-8324-20486a5adb05` | n/a | 0 | Pass | Inactive; complete v0.2.0 help. |
| `B12-final-footer` | 1 | `01a21206-a260-70f0-b351-b59566476179` | Yes | **0** | **Pass** | Self-contained `Checkpoint (strict, --normal) \| ...` in `last-message.txt`, 0 policy citations. |
| `B01-native` | 1 | `01a21205-c798-7383-8558-68866e6443ed` | Yes | 2 (`link`, `quote`) | Partial (UX) | Clean footer and transaction-ownership check; still emitted `[Seatbelt](.../SKILL.md)` + quote. |
| `B15-senior-calibration` | 1 | `01a21207-a0a5-7713-a477-8156b080a4bc` | Yes | 2 (`link`, `quote`) | Partial (UX) | Clean footer, skipped basics, targeted `create_user(connection, email=None)`; still emitted `[Seatbelt](.../SKILL.md)` + quote. |

## Claude Code `rc2` cohort (`v020-claude-20261125-034227`)

Skill hashes: `SKILL.md` `1d0b0f0a...`, `references/checkpoints.md` `11c3a064...`, `references/handoffs.md` `18201927...`. All 19 turns exited `0`, 0 timeouts, **0 UX findings** across the entire cohort.

| Case | Turns | Session ID | Functional | UX findings | Outcome | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| `smoke` (`--help`) | 1 | `a51b9b4b-bdb7-49f7-ab17-3efdc081bb0a` | n/a | 0 | **Pass** | Inactive; complete v0.2.0 help. |
| `B06-off-reset` | 2 | `bee5e57e-567a-475b-942e-3b6b5e6df128` | 14 unit + 5 acceptance pass | 0 | **Pass** | Plain `/seatbelt off` from `strict --terse` released checkpoint and restored normal prose. |
| `B06-off-terse` | 2 | `4bf8af4a-c443-40a3-80a0-8072b16afe3c` | 15 unit + 5 acceptance pass | 0 | **Pass** | `/seatbelt off --terse` from `strict --normal` released checkpoint and used terse prose. |
| `B08-terse` | 1 | `54ab6979-daa4-4564-abc7-ae821a148c65` | n/a (held) | 0 | **Pass** | 0 companion skills in `init.skills`; clear question + `Checkpoint (strict, --terse) \| ...`. |
| `B08-caveman-alias` | 1 | `9b27cf6b-69cc-40ff-9485-a4bfb1261426` | n/a (held) | 0 | **Pass** | 0 companion skills in `init.skills`; `--caveman` resolved to `--terse` in footer. |
| `B12-final-footer` | 1 | `f556b5fa-322b-45f7-a9f7-a4cfc12e8592` | n/a (held) | 0 | **Pass** | `result_text` itself carried `Checkpoint (strict, --normal) \| ...`; production files and DB untouched. |
| `B13-missing-checkpoints` | 1 | `cdd1c6f5-491c-4267-b851-38d02ff89a3e` | n/a (held) | 0 | **Pass** | Disclosed missing `references/checkpoints.md` in reply 1; held `app/`, `tests/`, `data/users.db`. |
| `B14-mentor` | 5 | `a7132dea-29c6-4ef5-8e0b-482327793187` | 13 unit pass | 0 | **Pass** | Non-spoiler prediction (naive draft results held back), `idk` hint, 2 misses -> explained + `Answer now` (no `"retry"` turn), immediate answer accepted, cleaned up `_scratch/`, closed with ownership limit + mentor takeaways. |
| `B15-senior-calibration` | 2 | `c595733e-56a7-4018-9bce-648dbd5a432a` | 11 unit pass | 0 | **Partial** (boundary nuance) / **Pass** (calibration + handoff) | Skipped basics, targeted `create_user(connection, email=None)` and held `data/users.sqlite3` + `app/users.py`, but wrote `app/migration.py` (with index recreation) in Turn 1 based on the user's explicit spec. Turn 2 `/seatbelt --handoff` emitted the exact copy-pasteable block with checkpoint still open. |
| `B16-multi-decision-divergence` | 2 | `94b2a602-c1ba-4f26-8520-dfc7031c684f` | 18 unit pass | 0 | **Pass** | Caught 429 contract note, paused on stranded `in_flight` delivery tradeoff, implemented and closed `standard` cleanly with explicit "What you now own" summary. |
| `B17-resumed` | 1 | `b6f4803d-d848-4c26-b7ec-07b646717c7d` | n/a (held) | 0 | **Pass** | Brand-new session in relocated copy restored `strict --compact` from `B15` Turn 2 `--handoff` and held `data/users.sqlite3` + `app/users.py`. |
