# Library crates

JMax is also a set of pure-Rust library crates you can embed directly — no
Python, no C++, no LAPACK/BLAS. Each crate is independently useful and
published to [crates.io](https://crates.io/crates/jmax).

| Crate | Purpose |
|---|---|
| `jmax-symbolic` | Computer-algebra engine: canonical term algebra, diff, e-graph simplify, integrate, solve. |
| `jmax-flowg` | flowG dataflow substrate: lowering, autodiff (grad/HVP/Hessian), optimization, ODE solvers. |
| `jmax-linalg` | Dense linear algebra: LU, Cholesky, QR, symmetric eigen, SVD, pseudoinverse. |
| `jmax-dsp` | FFT/IFFT, windows, spectrogram, FIR + IIR filtering. |
| `jmax-stat` | Distributions, RNG, and a parametric + nonparametric hypothesis-test suite. |
| `jmax-frame` | Typed dataframes with CSV and real Parquet I/O, group-by, pivot, joins. |
| `jmax-convex` | Linear, quadratic, and nonlinear constrained optimization. |
| `jmax-units` | Dimensional analysis and unit conversion. |
| `jmax-verify` | Formal-verification bridge: emit Lean 4 from symbolic results. |

Full embedding API: [docs.rs/jmax](https://docs.rs/jmax).
