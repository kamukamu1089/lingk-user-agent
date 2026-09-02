# lingk-user-agent

`lingk-user-agent` packages reusable guidance for configuring, building, running, and inspecting a local [`test_lingk`](../test_lingk) linear gyrokinetic simulation.

The domain workflow is stored once as an Agent Skill and is intended to work across Codex, Claude Code, and other clients that support the `SKILL.md` convention. Product-specific manifests are thin packaging adapters.

## Contents

- `skills/use-test-lingk/`: vendor-neutral workflow and focused parameter/output references
- `.codex-plugin/plugin.json`: Codex adapter
- `.claude-plugin/plugin.json`: Claude Code adapter
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

## Development loading

Claude Code can load this checkout as a plugin:

```bash
claude --plugin-dir /path/to/lingk-user-agent
```

Then invoke `/lingk-user-agent:use-test-lingk` explicitly or ask a matching `test_lingk` question.

For Codex, validate the checkout with the plugin validator described below, then use the current Codex local-plugin installation workflow. Marketplace metadata is intentionally not included in this initial repository scaffold.

Other Agent Skills clients can import or link `skills/use-test-lingk/` according to their current project- or user-skill conventions.

## Validation

```bash
python3 /path/to/skill-creator/scripts/quick_validate.py skills/use-test-lingk
python3 /path/to/plugin-creator/scripts/validate_plugin.py .
claude plugin validate .
```

The first two helper paths depend on the local Codex installation. Claude validation requires a current Claude Code installation.

## Source-of-truth boundary

The Skill provides decision guidance only. The `test_lingk` source, Makefile, input files, and any future standalone CLI remain the reproducible execution layer. Keep machine-specific paths, credentials, and research results out of this repository.
