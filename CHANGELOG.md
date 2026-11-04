# Changelog

## Unreleased

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

See the [trial report](docs/trials/2026-11-04/README.md) for exact configurations, revision coverage, and remaining gaps. These are small observed samples, not reliability or learning measurements. No release has been published yet.
