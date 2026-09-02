---
name: use-test-lingk
description: Configure, build, run, or inspect outputs from a local test_lingk linear gyrokinetic simulation. Use for test_lingk inputs, parameters, compilation, local execution, generated data, or bundled gnuplot visualization; do not claim physical validation or submit HPC jobs.
---

# Use test_lingk

Help the user operate an existing local checkout of `test_lingk` without making the language model part of the reproducible calculation.

## Locate the simulator

Use an explicit path from the user when provided. Otherwise search the current workspace, then adjacent directories, for a directory named `test_lingk` containing `Makefile`, `param.namelist`, and `src/lingk.f90`. Do not save a machine-specific absolute path in tracked files.

Before changing or running anything, inspect the repository status and the relevant input files. Treat existing source changes, `param.namelist`, `lingk.exe`, and `data/` contents as user data.

## Choose the operation

- For parameter questions or edits, read [references/parameters.md](references/parameters.md), `param.namelist`, and the current `src/parameters.f90`.
- For output inspection or visualization, read [references/outputs.md](references/outputs.md) and the relevant current source or plot script.
- For a build, check for `make` and the compiler selected by the current Makefile before running `make lingk`.
- For a run, first establish how outputs will be isolated from existing results.

## Safe workflow

1. Report the selected simulator path and whether relevant files already have local changes.
2. Explain whether each requested parameter is a runtime namelist value or a compile-time constant.
3. Preserve the original case. Prefer a separate case or temporary working directory containing the required executable, input, plot scripts, and an empty `data/` directory.
4. Show the exact parameter changes. Do not invent units, valid ranges, or physical interpretation that the sources do not establish.
5. Build with the current Makefile when compilation is required. Do not silently switch compilers or flags.
6. Run `./lingk.exe` from the isolated case directory because input and output paths are relative to the working directory.
7. Check the process exit status and expected output files. Distinguish successful execution and file-format checks from physical validation.
8. Summarize the input, source revision when available, build command, run command, output location, and checks performed.

## Safety boundaries

- Do not run `make clear`, `make clean`, recursive deletion, or overwrite existing results without explicit user authorization.
- Do not edit `src/parameters.f90` merely to change a value already available in `param.namelist`.
- Ask before starting a run expected to be long or resource-intensive. Stop after one failed retry unless a concrete, low-risk correction is evident.
- Do not submit scheduler jobs, access remote systems, or change credentials under this skill.
- Do not describe a case as converged or physically valid solely because the executable exited successfully or wrote files.
- Use gnuplot only when visualization is requested and the environment can support it. Avoid opening GUI windows without authorization.

## Handoff

Return concise reproduction details and identify any uncertainty. If the request needs reusable case creation, validation, batch submission, or structured analysis not provided by the current simulator, recommend implementing that behavior in an independent CLI rather than embedding it in this skill.
