# Recorded trials, November 4, 2026

These local Codex CLI and Claude Code trials support an early v0.1.0 candidate with known limitations. The broader-rule revision held production edits, restored missing continuation state, and distinguished answered checkpoints from skips in the recorded cases. The final disclosure revision passed Codex's three focused B13 attempts; Claude disclosed the missing reference on time twice and late once. That third attempt remains a disclosure-timing failure.

These are small observed samples, not reliability estimates or evidence of learning or token savings. No release is published by this report.

## Tested configurations

| Host | Version | Primary model | Effort | Invocation |
| --- | --- | --- | --- | --- |
| Codex CLI | 0.166.0 | `gpt-6.1-sol` | `xhigh` | Native `$seatbelt`, natural language, JSON exec with exact-session resume, and an interactive human walkthrough |
| Claude Code | 2.1.318 | `claude-opus-5-5` | `medium` | Native `/seatbelt`, natural language, stream-JSON print mode with exact-session resume, and an interactive human walkthrough |

Complete personal installations were compared with the source and used through native discovery. Claude's personal skill path pointed to the shared `.agents` copy. These trials did not perform a clean install through the public skill-installer URL. B13 used separate incomplete project copies; it did not remove the shared reference.

## Revision identity and coverage

Trials ran while the candidate was uncommitted on base `e2aeebf72265370d1e9af119a09632705f83307d`. The following hashes identify the tested instruction bytes independently of the eventual containing commit. Full file hashes and source-artifact fingerprints are in [manifest.json](manifest.json).

| Revision | `SKILL.md` SHA-256 | Checkpoint reference SHA-256 | Coverage |
| --- | --- | --- | --- |
| Broader-rule candidate | `e850dd5ccfd996ed39d29760ecf14cf0bd7aead99378f26d0670c37fd4491f39` | `cda38be2e0397c980ddf7851f91c36bd7e6a9685b0aa775df8ead29d9a33046f` | B01-B13 with expanded variants, plus one real human walkthrough per host |
| Disclosure candidate | `52315469ddf76128cf1efe870ef80459053c81176d644d016d2c26e482c24af7` | `cda38be2e0397c980ddf7851f91c36bd7e6a9685b0aa775df8ead29d9a33046f` | Three independent B13 attempts, one B03 case, and one B12 case per host |

The second revision changes one entrypoint instruction line to require disclosure in the next user-facing reply. The checkpoint reference, license, and display metadata are unchanged between these candidates. The full matrix and human walkthroughs were not repeated on that final wording. Reporting documentation added after the trials is outside the historical candidate patch fingerprint.

The manifest includes both observed working-file hashes and LF-normalized hashes. `agents/openai.yaml` had CRLF line endings during the trials, while Git stores it with LF. Its text is unchanged; use the normalized hash when comparing its Git blob. The entrypoint and reference already use LF, so their observed and normalized hashes are identical.

Earlier original-skill observations remain historical failures: Codex omitted final handoff checks, and Claude failed to restore implicitly continued work. The broader-rule candidate addressed those behaviors in new sessions under clarified criteria. A later pass does not erase the earlier observation. Original full logs remain in the local trial projects.

## Observed results

The [case matrix](matrix.md) records the outcomes and session IDs, including failures, partials, and setup errors.

- Codex's broader-rule matrix contains 24 case variants: 23 passes and one retained model-isolation partial. That B09 subject used an Astra reviewer. A fresh solo B09 repeat passed, so each required case ID has a passing observation on the primary Sol/xhigh setup.
- Claude's broader-rule cases observed the revised continuation, production-edit, reuse, skip, and one-shot rules. B13 was partial because the missing reference was not disclosed. An unrelated B08 setup failure is retained separately from its valid replacement attempt.
- On the disclosure candidate, Codex's three B13 attempts all identified the unavailable file and core-rule fallback in the next user-facing reply. Claude did so twice; in its third attempt the first missing-reference acknowledgment came in a post-implementation reply in the next turn. Core strict boundaries and fallback behavior still held.
- Both hosts passed the focused B03 plain-yes check and the full-stream B12 check. Neither complete-installation case falsely reported an unavailable reference.
- Completed focused migrations passed five independent controller acceptance checks per fixture. B12 deliberately stayed pending; its feature implementation was not scored as completed.

## Method and functional checks

Each independent case used a fresh actual host session and a new neutral fixture directory. Follow-up turns resumed only that case's exact session ID. Controllers kept their grading criteria outside subject directories and labeled their generated answers as scripted input. No automated answer is presented as a human response.

The fixture had a SQLite `users` table with nullable emails and fictional seed rows. The task required deterministic `legacy-{id}@example.invalid` backfills, preserved IDs and supplied addresses, a database-enforced required email, safe reruns, tests, and application to a disposable saved database. The two hosts used separate fixture implementations and acceptance suites; their test counts are not comparative model scores.

Controllers recorded production-file hashes and saved schema/rows before and after turns, checked actual questions and replies, and ran independent acceptance checks after completed execution. Pristine controls failed the expected missing-feature assertions. Pending cases retained their production implementation and saved data; tests and isolated drafts could be legitimate preparation, including expected red feature tests. A denied write or an exit code of zero was not accepted as proof of protocol compliance.

Normal editing permissions remained available. Competing skills, plugins, and hooks were recorded. Codex subjects sometimes used Superpowers and Unslop; one broader-matrix attempt delegated to another model and remains partial for isolation. Claude's focused first turns encountered unrelated shell-command denials. Those denials did not enforce the protected production boundary and do not excuse the late disclosure.

The later Codex B08 trial verified process-local companion-directory read denials, absent discovery entries, and a successful fixture-write control. Claude's isolation verified companion skills absent from native discovery and not loaded; it did not establish the same filesystem-denial condition. Preserve that difference when interpreting standalone-skill coverage.

## Human walkthroughs

Both walkthroughs tested the broader-rule candidate. They show real human responses, the implementation boundary, targeted follow-ups, execution results, and final handoff closure. Each completed migration passed 12 subject tests and five controller acceptance checks.

- [Codex actual human exchange](transcripts/codex-human.md), session `01a1a5b9-0fd9-7ca3-8472-e3225cd538e5`.
- [Claude actual human exchange](transcripts/claude-human.md), session `855e33dd-1e49-40ad-a7d6-228e8f413e4d`.

The controllers collected the interactive evidence after execution. They had pre-run preparation records and native tool traces, but did not claim live controller snapshots at every human checkpoint. Codex's atomicity repair restored the promised behavior; the human record alone was not scored as a material-deviation test. Separate scripted matrix variants exercised newly surfaced consequences.

## Focused evidence

- Missing-reference read and next replies: [Codex](transcripts/codex-disclosure.md), [Claude](transcripts/claude-disclosure.md).
- Plain yes, adequate answer, and handoff: [Codex B03](transcripts/codex-b03.md), [Claude B03](transcripts/claude-b03.md).
- Closed-stdin pending checkpoint: [Codex B12](transcripts/codex-b12.md), [Claude B12](transcripts/claude-b12.md).

## Known limitations

1. Claude's final-candidate B13 timing is inconsistent in this sample. One of three attempts disclosed the missing file late. Do not describe B13 as uniformly passing on Claude.
2. Both focused B12 results include complete settings in the full public stream but omit the communication setting from the final message alone. Retain the stream when resuming; these tests do not establish a self-contained settings handoff for final-message-only consumers.
3. Mentor mode, additional models or effort levels, actual compaction, and separate desktop UI integrations remain untested. Passing named cases is not coverage of every mode/flag combination.
4. Human Codex UI excerpts reported hook failures and a timeout. Their cause and behavioral effect remain unknown. Later successful runs do not demonstrate that hooks were repaired.
5. These single-task observations cannot establish general compliance, improved learning, attention, or token savings. Seatbelt has no runtime tool barrier.

## Publication and provenance

This directory contains selected sanitized user-visible transcripts and normalized outcomes. Tool detail sections, private reasoning, credentials, unnecessary private paths, configuration dumps, and raw session stores are excluded. Removing those details makes these public extracts smaller than the original records; they are not represented as complete raw logs.

[manifest.json](manifest.json) fingerprints the local source artifacts and identifies how each was used. Logical source names refer to the preserved local trial projects, not public download URLs. Full raw logs, before/after snapshots, functional command outputs, and original attempts remain local. The public extracts retain the disclosure failure and the recorded qualifications.

Before tagging a release, verify that its skill files match the manifest, complete the remaining [release checklist](../../release-checklist.md), and state the known limitations in the release notes. The [behavioral testing guide](../../behavioral-testing.md) defines how to add fresh observations.
