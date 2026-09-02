# test_lingk parameters

Use the checked-out source as the authority. This reference describes the current sample repository and must not override later source changes.

## Runtime namelist

`param.namelist` contains the `physp` namelist read by `parameters:set_param`.

| Parameter | Current sample | Source-level role |
| --- | ---: | --- |
| `kx` | `0.0` | Perpendicular Fourier mode component |
| `ky` | `0.2` | Perpendicular Fourier mode component |
| `eps_r` | `0.18` | Geometry parameter used by the s-alpha model |
| `q_0` | `1.4` | Safety-factor parameter |
| `s_hat` | `0.8` | Magnetic-shear parameter |
| `lambda` | `0.0` | Parameter accepted by the current model |
| `beta` | `0.0` | Electromagnetic beta parameter |
| `R0_Ln` | `2.2` | Species density-gradient parameter |
| `R0_Lt` | `6.9` | Species temperature-gradient parameter |
| `nu` | `0.0` | Species collisionality parameter |
| `Anum` | `1.0` | Species mass-number parameter |
| `Znum` | `1.0` | Species charge-number parameter |
| `fcs` | `1.0` | Species fraction parameter |
| `sgn` | `1.0` | Species sign parameter |
| `tau` | `1.0` | Species temperature-ratio parameter |

The source and README do not fully document units, normalization, safe ranges, or all physical meanings. Inspect the equations and ask the project maintainer when those details affect a scientific conclusion.

The species-valued entries are arrays sized by compile-time `ns`. Sample one- and two-species parameter files exist under `sample_param/`, but copying one is not sufficient by itself: `src/parameters.f90` must have a consistent `ns` and must be rebuilt.

## Compile-time parameters

The current `src/parameters.f90` defines these as Fortran `parameter` constants:

| Name | Current value | Effect |
| --- | ---: | --- |
| `litime` | `1000` | Time-iteration limit |
| `elt_limit` | `120` | Elapsed-time limit in seconds |
| `time_limit` | `10` | Simulation-time limit |
| `nz` | `24*5` | Parallel/spatial grid half-size convention used by arrays and output |
| `nv` | `32` | Parallel-velocity grid parameter |
| `nm` | `31` | Magnetic-moment grid parameter |
| `ns` | `1` | Number of species |
| `nzb` | `2` | Boundary/grid-related compile-time value; confirm in source before changing |
| `nvb` | `2` | Boundary/grid-related compile-time value; confirm in source before changing |
| `lz` | `5*pi` | Spatial domain half-size |
| `lv` | `4` | Parallel-velocity domain half-size |
| `lm` | `8` | Magnetic-moment domain maximum |
| `dt_out` | `0.1` | Output interval |
| `flag_dtc` | `.true.` | Time-step control flag |
| `flag_runs` | `1` | Output run suffix selector |

`dt` currently starts at `0.01` but is not declared as a Fortran `parameter`; its runtime behavior depends on `flag_dtc` and the geometry/time-step control code.

Changing this file requires rebuilding. Plot scripts currently duplicate values such as `nz`, `dt_out`, and output indices, so review them after changing compile-time values.

## Editing checks

- Preserve valid Fortran namelist syntax and the `&physp` / `&end` delimiters.
- Match the number of species values to `ns`.
- Keep a record of both `param.namelist` and the compiled `src/parameters.f90`.
- Never infer that a numerically accepted value is scientifically appropriate.
