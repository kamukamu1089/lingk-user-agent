# test_lingk outputs

The executable opens output paths relative to its working directory. Ensure an existing `data/` directory is not reused unintentionally.

## Files

| Path pattern | Format | Current contents | Bundled viewer |
| --- | --- | --- | --- |
| `data/frq.NNN` | Text | Time, growth rate, frequency, successive differences, and a field-equation inequality diagnostic | `plot_linfreq.gn` |
| `data/mominzt.NNN` | Text, blocks by time | `z`, time, complex field values, and complex density moments for each species | `plot_mominz.gn` |
| `data/fkinzv_imMMMM_tTTTTTTTT.dat` | Fortran unformatted direct-access binary | `z`, parallel velocity, and complex distribution function values for each species at one magnetic-moment index | `plot_fkinzv.gn` |

`NNN` comes from the compile-time `flag_runs`; the current value `1` produces `.001`.

## `frq.NNN`

The current header written by `src/fileio.f90` names these columns:

1. `time`
2. `growth`
3. `frequency`
4. `diff(growth)`
5. `diff(freq.)`
6. `1-Ineq.`

When the solver's internal convergence condition is met, it appends a `# Well converged.` section with `kx`, `ky`, growth rate, frequency, and the inequality diagnostic. Report this as the solver's own criterion, not as an independent physical validation.

## `mominzt.NNN`

The header begins with `zz`, `time`, `phi`, `Al`, and `dens`. Complex values are written as real and imaginary components by formatted Fortran output. Blank records separate time blocks. Check the current source before assigning exact column numbers, especially when `ns` changes.

## Distribution-function binary output

The current direct-access records contain double-precision values for `zz`, `vl`, and the real and imaginary parts of `fk` for every species. Record layout therefore depends on compile-time grid and species settings. The bundled gnuplot script currently assumes values such as `nz = 24*5`, `im = 7`, `dt_out = 0.1`, and a one-species binary format.

Do not reuse that plotting format blindly after changing `nz`, `ns`, `nm`, or `dt_out`.

## Inspection checklist

1. Confirm the executable exit status.
2. List newly created files without deleting older ones.
3. Confirm expected text files are non-empty and inspect their headers.
4. Check whether `frq.NNN` contains the solver's convergence marker when convergence is relevant.
5. Treat binary file size and layout checks as format checks only.
6. Record the input, source revision, compiler/build flags, working directory, and output directory needed to reproduce the run.
