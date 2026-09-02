# lingk-user-agent

`lingk-user-agent` packages reusable guidance for configuring, building, running, and inspecting a local [`test_lingk`](../test_lingk) linear gyrokinetic simulation.

The domain workflow is stored once as an Agent Skill and is intended to work across Codex, Claude Code, and other clients that support the `SKILL.md` convention. Product-specific manifests are thin packaging adapters.

## Contents

- `skills/use-test-lingk/`: vendor-neutral workflow and focused parameter/output references
- `.codex-plugin/plugin.json`: Codex adapter
- `.claude-plugin/plugin.json`: Claude Code adapter
- `.claude-plugin/marketplace.json`: Claude Code marketplace manifest (lets this checkout be installed via `claude plugin install`)
- `.agents/plugins/marketplace.json`: Codex repo/team marketplace manifest (lets this checkout be installed via `codex plugin add`)
- `AGENTS.md`: repository development and logging conventions
- `CLAUDE.md`: Claude Code development-session pointer to `AGENTS.md`
- `devlog/`: implementation plans and reports

## Current capability

Use the skill when a user asks to:

- understand or edit `test_lingk` inputs;
- distinguish runtime namelist values from compile-time constants;
- build the solver with its current Makefile;
- run a local case without overwriting existing results;
- locate and inspect standard outputs;
- use the bundled gnuplot scripts when visualization is explicitly requested.

The initial version does not submit HPC jobs, manage remote systems, guarantee convergence or physical validity, or replace the simulator with model-generated calculation logic.

## Installing for Claude Code

This repository is also a local marketplace of one plugin (`.claude-plugin/marketplace.json`, source type `directory`). Register it once, then install and enable the plugin:

```bash
claude plugin marketplace add /path/to/lingk-user-agent --scope project
claude plugin install lingk-user-agent@lingk-user-agent-marketplace --scope project
```

`--scope project` writes `extraKnownMarketplaces` and `enabledPlugins` to the calling project's `.claude/settings.json`, so the plugin loads automatically for future sessions started in that project. Use `--scope user` instead to enable it for every project. Toggle later with `claude plugin enable|disable lingk-user-agent@lingk-user-agent-marketplace`.

For ad hoc, session-only loading without touching any settings file, Claude Code can also load this checkout directly:

```bash
claude --plugin-dir /path/to/lingk-user-agent
```

Either way, invoke `/lingk-user-agent:use-test-lingk` explicitly or ask a matching `test_lingk` question.

## Installing for Codex

This repository is also a Codex repo/team marketplace of one plugin (`.agents/plugins/marketplace.json`, source type `local`). Codex does not discover a repo/team marketplace implicitly the way it does the personal marketplace at `~/.agents/plugins/marketplace.json`, so register it explicitly first:

```bash
codex plugin marketplace add /path/to/lingk-user-agent
codex plugin add lingk-user-agent@lingk-user-agent-marketplace
```

Start a new Codex thread afterward so it picks up the plugin's skill. Do not hand-edit `.agents/plugins/marketplace.json` for routine updates while iterating locally; use the plugin-creator skill's cachebuster/reinstall flow (`scripts/read_marketplace_name.py`, `scripts/update_plugin_cachebuster.py`) instead, pointed at this repo's marketplace path.

Other Agent Skills clients can import or link `skills/use-test-lingk/` according to their current project- or user-skill conventions.

## Validation

```bash
python3 /path/to/skill-creator/scripts/quick_validate.py skills/use-test-lingk
python3 /path/to/plugin-creator/scripts/validate_plugin.py .                         # Codex plugin manifest
python3 /path/to/plugin-creator/scripts/read_marketplace_name.py \
  --marketplace-path .agents/plugins/marketplace.json                               # Codex marketplace manifest
claude plugin validate .claude-plugin/plugin.json   # Claude Code plugin manifest
claude plugin validate .                            # Claude Code marketplace manifest (dir form checks marketplace.json when present)
```

The first two helper paths depend on the local Codex installation (`plugin-creator` system skill). Claude validation requires a current Claude Code installation.

## Source-of-truth boundary

The Skill provides decision guidance only. The `test_lingk` source, Makefile, input files, and any future standalone CLI remain the reproducible execution layer. Keep machine-specific paths, credentials, and research results out of this repository.
