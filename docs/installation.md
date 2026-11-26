# Installation

Install the complete [`skills/seatbelt`](../skills/seatbelt) folder. Keep these files together:

```text
seatbelt/
  SKILL.md                    Protocol, modes, flags, and checkpoint rules.
  LICENSE                     MIT license for redistributed copies.
  agents/openai.yaml          Optional Codex display metadata.
  references/checkpoints.md   Question patterns, calibration, and evaluation.
  references/handoffs.md      Handoff template, recovery, and one-shot format.
```

No scripts, packages, or MCP servers are required. Grill-Me and Caveman are not prerequisites; Seatbelt defines its own questioning and terse-prose rules.

## Codex

Ask Codex to use its built-in skill installer:

```text
Use $skill-installer to install the seatbelt skill from
https://github.com/LunarianDev/cognitive-seatbelt/tree/main/skills/seatbelt
```

For manual installation, copy the complete `skills/seatbelt` folder to one of these locations, then start a fresh session:

| Scope | macOS / Linux | Windows |
| --- | --- | --- |
| Personal | `~/.agents/skills/seatbelt/` | `%USERPROFILE%\.agents\skills\seatbelt\` |
| Project | `.agents/skills/seatbelt/` inside the repo | `.agents\skills\seatbelt\` inside the repo |

Installer-managed copies may live under `$CODEX_HOME/skills/seatbelt` (default `~/.codex/skills/seatbelt` or `%USERPROFILE%\.codex\skills\seatbelt`). Avoid keeping duplicate copies named `seatbelt` in two discovery directories at once.

Invoke Seatbelt with `$seatbelt` followed by your controls and task. See the [official Codex skills documentation](https://learn.chatgpt.com/docs/build-skills) for host discovery rules.

## Claude Code

For a local Claude Code session, copy the complete `skills/seatbelt` folder to one of these locations, then start a fresh session:

| Scope | macOS / Linux | Windows |
| --- | --- | --- |
| Personal | `~/.claude/skills/seatbelt/` | `%USERPROFILE%\.claude\skills\seatbelt\` |
| Project | `.claude/skills/seatbelt/` inside the repo | `.claude\skills\seatbelt\` inside the repo |

Invoke Seatbelt with `/seatbelt`. Cloud and Cowork sessions use different skill discovery rules; see the [official Claude Code skills documentation](https://code.claude.com/docs/en/skills) for those environments.

`agents/openai.yaml` is optional Codex display metadata. Other hosts do not need it; behavior lives in `SKILL.md` and the `references/` files.

## Optional: `npx skills add` CLI

If you already manage Agent Skills with the open-source `skills` CLI (requires Node.js), you can install from `main` with:

```bash
npx skills add LunarianDev/cognitive-seatbelt --skill seatbelt
```

Add `--global` (or `-g`) for a personal installation instead of the current repository, and `--agent codex` or `--agent claude-code` to target a specific host. Start a fresh session afterward. Note that `npx skills add` is a third-party CLI tool and was not part of the recorded manual-copy trials.

## Verify in 10 seconds

Open a fresh chat session in your host and run:

- Codex: `$seatbelt --help`
- Claude Code: `/seatbelt --help`

You should get a short reference showing the five modes, communication flags (`--terse` with `--caveman` alias), defaults (`standard --adaptive`), reply shortcuts (`idk`, `hint`, `explain`, `skip`), and `--status` / `--handoff`. Running `--help` confirms discovery without activating checkpoints.

If the host does not recognize the skill after copying files manually, confirm `SKILL.md` sits directly inside `seatbelt/` (not nested in an extra subfolder) and restart the session.

## Other compatible agents

For any host supporting the [Agent Skills format](https://agentskills.io/specification), copy the complete folder into its skill directory, start a fresh session, and use its native skill invocation. Packaging compatibility does not establish behavioral reliability.

If a host has no skill loader, provide `SKILL.md`, `references/checkpoints.md`, and `references/handoffs.md` directly as task instructions. Interactive checkpoints require the host to retain chat state and pause for a human reply.

## Updates and development branches

Check an existing installation before replacing it. The Codex installer stops rather than overwriting an installed skill. Back up local edits and replace the entire `seatbelt/` folder together so `SKILL.md` and `references/` stay aligned.

The quick-start URL targets `main`. To test a development branch whose name contains a slash (such as `dev/v0.2.0`), pass repo, path, and ref separately:

```text
Use $skill-installer to install seatbelt from repo LunarianDev/cognitive-seatbelt,
path skills/seatbelt, with ref dev/v0.2.0.
```

For manual testing, copy the folder from the branch or commit you are testing, record that commit in your trial notes, and avoid leaving a second copy under the same name.

If a bundled reference (`references/checkpoints.md` or `references/handoffs.md`) is missing, Seatbelt should name the unavailable file in its next user-facing reply and fall back to the core rules in `SKILL.md`. Restore the complete folder for full guidance.
