# Repository instructions

## Project context

- This repository packages reusable, vendor-neutral agent guidance for using `test_lingk`.
- Keep domain knowledge and workflows in `skills/`. Keep Codex-, Claude Code-, and other client-specific files as thin adapters.
- Do not duplicate simulation logic in agent instructions. Treat the simulator, its Makefile, and any future CLI as the executable source of truth.
- Preserve existing inputs and results unless the user explicitly authorizes replacing or deleting them.

## Planning and work logs

- For every implementation task, create or update an appropriate plan and work report under `devlog/`.
- Use the task's start date and a concise descriptive name:
  - `devlog/YYYYMMDD_plan_<descriptive-name>.md`
  - `devlog/YYYYMMDD_report_<descriptive-name>.md`
- For work started on 2026-09-02, examples are `devlog/20260902_plan_test_lingk-agent-plugin.md` and `devlog/20260902_report_test_lingk-agent-plugin.md`.
- Write plans before implementation when the task is non-trivial. Keep the report current during implementation and record changed files, verification performed, results, limitations, and follow-up work.
- Do not overwrite an unrelated existing log. Reuse a matching task log when continuing the same work.

## Validation

- Validate shared skills independently of product adapters.
- Validate each product-specific manifest with that product's current official validator when available.
- Record skipped checks and their reason in the work report.
