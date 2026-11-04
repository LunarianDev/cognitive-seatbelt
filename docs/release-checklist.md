# First release checklist

Target title: **Cognitive Seatbelt v0.1.0: Stay in the Loop**.

This is release preparation, not a published release or a record of completed behavioral trials. Logo work is tracked separately.

## Repository presentation

- [x] Lead the README with the problem, value, and an illustrative workflow.
- [x] Keep the quick start short and link detailed installation and controls.
- [x] Keep cognitive participation and communication length separate.
- [x] Move expected behavioral cases into a dedicated testing guide.
- [x] Add an MIT license and include it in the installable skill folder.
- [x] Add contribution guidance, issue forms, and a pull request template.
- [ ] Merge the reviewed documentation changes into `main`.

The checkmarks describe files prepared on the development branch; they do not mean those files are on `main` yet.

## GitHub presentation

Suggested description:

> Stay in the loop while coding with AI agents. Lightweight checkpoints for understanding decisions, not micromanaging execution.

Suggested topics: `agent-skills`, `ai-agents`, `ai-assisted-development`, `claude-code`, `codex`, `developer-tools`, `human-in-the-loop`.

- [x] Set the repository description and topics to match the current project.
- [x] Point the About website link at the README until a separate homepage exists.
- [ ] Confirm GitHub recognizes MIT after the license reaches the default branch.
- [ ] Add the separately prepared visual identity when ready.

## Evidence before promotion

- [ ] Follow the documented installation path in a fresh Codex session.
- [ ] Follow the documented installation path in a fresh local Claude Code session.
- [ ] Publish a sanitized representative trial from each host, using the [trial record template](behavioral-testing.md#trial-record-template).
- [ ] Record the exact model, host version, skill commit, and observed outcomes.
- [ ] Check that public claims distinguish packaging support, observed behavior, and untested cases.

The README example is illustrative and cannot satisfy these trial items. Logo work does not block behavioral testing.

## Publish v0.1.0

- [ ] Review the final changes and documentation links.
- [ ] Replace the [Unreleased changelog](../CHANGELOG.md#unreleased) heading with the version and actual release date.
- [ ] Create a `v0.1.0` tag on the reviewed commit from `main`.
- [ ] Publish release notes describing the instructions-only design, five participation modes, independent communication controls, and installation folder.
- [ ] Link recorded trials and disclose the remaining behavioral testing gaps.
- [ ] Verify the release links and installation instructions from the public repository.

The initial release should make no claims of measured learning benefits, token savings, runtime tool blocking, or universal model compliance.
