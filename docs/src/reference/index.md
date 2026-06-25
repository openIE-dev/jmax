# Reference

The complete JMax surface, in three parts. Everything here also lives on the
website — these pages mirror [charlot-lang.dev](https://charlot-lang.dev).

- **[Built-in functions](./builtins.md)** — 113 functions across 15 domains
  (linear algebra, statistics, signal processing, ML, symbolic math, calculus
  & autodiff, optimization, ODEs, hypothesis tests, dataframes, units, and
  plotting). The same set is browsable at
  [docs.charlot-lang.dev](https://docs.charlot-lang.dev).
- **[The `jmax` command line](./cli.md)** — one binary across the whole
  scientific-computing surface: `eval`, `run`, `plot`, `grad`, `minimize`,
  `ode`, `fit`, `verify`, and `emit` to ONNX / StableHLO / WGSL / MLIR.
  Mirrors [api.charlot-lang.dev](https://api.charlot-lang.dev).
- **[Library crates](./libraries.md)** — embed JMax directly from Rust; pure
  Rust, no Python or C++ dependencies.

## Try it without installing

Every built-in runs in the browser at
[play.charlot-lang.dev](https://play.charlot-lang.dev) — the JMax evaluator
compiled to WebAssembly. Nothing leaves the page.

## Embedding API

For the Rust embedding API (types, traits, and per-crate items), see
[docs.rs/jmax](https://docs.rs/jmax).
