# Installation

Install the complete [`skills/seatbelt`](../skills/seatbelt) folder. Keep these files together:

```text
seatbelt/
  SKILL.md                    Protocol, modes, flags, and checkpoint rules.
  LICENSE                     MIT license for redistributed copies.
  agents/openai.yaml           Optional Codex display metadata.
  references/checkpoints.md    Question patterns and evaluation guidance.
```

No scripts, packages, or MCP servers are needed. Grill-Me and Caveman are not prerequisites; Seatbelt contains its own questioning and communication rules.

## Codex

Ask Codex to use its skill installer:

```text
Use $skill-installer to install the seatbelt skill from
https://github.com/LunarianDev/cognitive-seatbelt/tree/main/skills/seatbelt
```

For manual installation, copy the complete folder to one of these locations:

| Scope | Destination |
| --- | --- |
| Personal | `~/.agents/skills/seatbelt/` |
| Project | `.agents/skills/seatbelt/` inside the repository |

`~` means your home directory. Installer-managed copies may use `$CODEX_HOME/skills/seatbelt`, defaulting to `~/.codex/skills/seatbelt`. Avoid duplicate copies with the same skill name. Check that `seatbelt` appears in your host's skill selector before invoking it.

Use `$seatbelt` followed by your controls and task. See the [official Codex skills documentation](https://learn.chatgpt.com/docs/build-skills) for discovery locations and host setup.

## Claude Code

For a local Claude Code session, copy the complete folder to one of these locations:

| Scope | Destination |
| --- | --- |
| Personal | `~/.claude/skills/seatbelt/` |
| Project | `.claude/skills/seatbelt/` inside the repository |

Invoke it with `/seatbelt`. Cloud and Cowork sessions have different skill discovery rules; see the [official Claude Code skills documentation](https://code.claude.com/docs/en/skills) for those environments.

`agents/openai.yaml` is optional Codex display metadata. Other hosts do not need to interpret it. The behavior is defined by `SKILL.md` and the checkpoint reference.

## Other compatible agents

For a host that supports the [Agent Skills format](https://agentskills.io/specification), copy the complete folder into its supported skill location and use its invocation mechanism. Discovery locations and syntax vary by host. Packaging compatibility does not establish behavioral reliability.

If a host has no skill loader, explicitly provide `SKILL.md` and `references/checkpoints.md` as task instructions. If it cannot read local files, include their text in the task context. It must retain conversational state and return a pending question to a human for interactive checkpoints to work. Instructions remain subject to the host's own permissions and higher-priority rules.

## Updates and development versions

Review an existing installation before replacing it. The Codex installer stops rather than overwriting an existing skill. Back up local customizations and replace the complete folder together so the entrypoint and reference stay in sync.

The quick-start URL targets `main`. To try the development branch, specify the repository, path, and ref separately because the branch name contains a slash:

```text
Use $skill-installer to install seatbelt from repo LunarianDev/cognitive-seatbelt,
path skills/seatbelt, with ref codex/dev.
```

For manual setup, copy the folder from the branch or commit you intend to test. Record that commit in behavioral trial results, and avoid installing a second copy under the same name.

If the bundled reference is missing, Seatbelt should disclose that and follow the core checkpoint rules in `SKILL.md`. Restore the reference to use the full question-design guidance.
