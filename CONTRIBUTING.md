# Contributing

Reports from real tasks help improve Cognitive Seatbelt. Useful examples include a checkpoint that clarified a decision, interrupted unnecessarily, accepted an inadequate answer, or lost track of settings.

## Report behavior or suggest a change

[Open an issue](https://github.com/LunarianDev/cognitive-seatbelt/issues/new/choose). For a behavior report, include:

- The host and version, model, and skill commit or installation source.
- The invocation, mode, communication flag, and relevant active instructions.
- A small reproduction with expected and observed behavior.
- A sanitized excerpt showing the checkpoint and what happened next.

Exclude credentials, private code, personal data, and client information. See the [behavioral testing guide](docs/behavioral-testing.md) for cases and a trial record template. Expected behavior alone is not a test result.

For a suggestion, describe the task and the problem you want solved. A concrete example is more useful than a new mode or flag without a use case.

## Send a pull request

Keep a change focused and explain what it changes for the person using the skill. For protocol changes, describe the checkpoint boundary, expected behavior, and tradeoff. Update documentation and behavioral scenarios when they change.

Preserve the distinction between participation and communication. Keep checkpoint friction proportional to the task, and make the instructions usable across compatible hosts. Do not introduce scripts, dependencies, or a runtime enforcement claim without discussing the change first.

Before submitting:

1. Check that local Markdown links resolve and examples match the skill instructions.
2. Run `git diff --check` to catch whitespace errors.
3. For behavior changes, run applicable [trial cases](docs/behavioral-testing.md) in fresh sessions and report the host, model, and evidence. State what you could not test.
4. Confirm the complete skill folder still includes its reference and license.

Documentation-only edits need documentation checks; they do not establish model compliance. Keep illustrative dialogue labeled as such.

## License

Contributions are provided under the repository's [MIT license](LICENSE).
