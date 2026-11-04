# v0.1.0 release checklist

Target title: **Cognitive Seatbelt v0.1.0: Stay in the Loop**.

Release date: November 4, 2026. The [GitHub v0.1.0 release](https://github.com/LunarianDev/cognitive-seatbelt/releases/tag/v0.1.0) records publication status, the versioned archive, and its checksum. [Recorded behavioral trials](trials/2026-11-04/README.md) include the accepted B13/B12 limitations. Logo work is tracked separately.

## Repository presentation

- [x] Lead the README with the problem, value, and an illustrative workflow.
- [x] Keep the quick start short and link detailed installation and controls.
- [x] Keep cognitive participation and communication length separate.
- [x] Move expected behavioral cases into a dedicated testing guide.
- [x] Add an MIT license and include it in the installable skill folder.
- [x] Add contribution guidance, issue forms, and a pull request template.
- [x] Merge the reviewed documentation changes into `main` through PR #1.

The preparation checks describe the source merged into `main`. Publication checks below use the release tag and GitHub release record.

## GitHub presentation

Suggested description:

> Stay in the loop while coding with AI agents. Lightweight checkpoints for understanding decisions, not micromanaging execution.

Suggested topics: `agent-skills`, `ai-agents`, `ai-assisted-development`, `claude-code`, `codex`, `developer-tools`, `human-in-the-loop`.

- [x] Set the repository description and topics to match the current project.
- [x] Point the About website link at the README until a separate homepage exists.
- [x] Confirm GitHub recognizes MIT on the default branch.
- [ ] Add the separately prepared visual identity when ready.

## Evidence before promotion

- [x] Verify complete personal skill installation and native discovery in fresh Codex CLI sessions.
- [x] Verify complete personal skill installation and native discovery in fresh local Claude Code sessions.
- [x] Include sanitized representative trials from each host, with real human answers labeled separately from scripted inputs.
- [x] Record exact models, host versions, tested skill hashes, base commit, and observed outcomes.
- [x] Distinguish packaging support, observed behavior, untested cases, and the accepted B13/B12 limitations in public documentation.
- [x] Recheck the quick-start installer URL on merged `main` in an isolated destination and compare the packaged skill with the tested manifest.

The README example is illustrative and cannot satisfy these trial items. Logo work does not block behavioral testing.

## Publication verification

The tag and GitHub release are the publication records. Verify them against these requirements:

1. The [changelog](../CHANGELOG.md) identifies version 0.1.0 and November 4, 2026.
2. The annotated `v0.1.0` tag targets the release commit on `main`, whose skill files match the tested manifest.
3. The release includes the complete `seatbelt/` folder in `seatbelt-v0.1.0.zip` and a `SHA256SUMS.txt` file for that archive.
4. Release notes describe the instructions-only design, five participation modes, independent communication controls, and pinned installation URL.
5. Release notes link the trial report and disclose Claude's late B13 disclosure, B12's final-message-only settings gap, and the remaining coverage limits.
6. The public tag, installer path, archive contents, checksum, and release links resolve and match the frozen source.

The initial release should make no claims of measured learning benefits, token savings, runtime tool blocking, or universal model compliance.
