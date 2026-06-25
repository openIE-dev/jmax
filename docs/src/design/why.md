# Why JMax

> Write the math. See the result.

Scientific computing fractured into a stack you assemble before you can begin:
a language, a numerics library, a plotting library, an autodiff package, an
optimizer, a dataframe, a unit system — each a separate install, each with its
own conventions, none of them aware of the others. JMax collapses that stack
into one binary where the pieces already fit.

## Three ideas

**Matrices are values.** A matrix, a vector, a symbolic expression, a
dataframe — these are first-class values with the operators mathematics already
gives them. `A \ b` solves a system; `*` multiplies matrices; `'` transposes;
`d/dx` differentiates. There is no array library to import because arrays are
the language.

**Plots are statements.** Visualization isn't a bolted-on package with its own
object model. `plot line xs, ys` is a statement, the same way `let` is. One
Scene IR drives publication SVG, native-GPU rendering, and an interactive
WebGPU canvas — line, scatter, bar, histogram, density, heatmap, boxplot,
violin, contour, polar, parallel-coordinates, 3-D scatter, and 3-D surface, plus
PCA / t-SNE / UMAP projections.

**Every computation is measured in joules.** Energy is a first-class
observable. Every workload can attach a measured receipt — picojoules per
operation, with the sensor source — because JMax is projected onto
[flowG](https://github.com/openIE-dev/flow-g), the energy-metered substrate.
The same code runs on CPU, Apple-silicon Metal, or WebGPU, and reports what it
cost.

## What that buys you

- **113 built-in functions** across 15 domains — linear algebra, statistics,
  signal processing, ML primitives, symbolic math, calculus & autodiff,
  optimization, ODEs, hypothesis tests, dataframes, units, and plotting —
  with nothing else to install.
- **Exact where it can be.** Symbolic differentiation, integration, and
  simplification (equality-saturation e-graph); identities you can `verify`
  into a Lean 4 theorem.
- **The same code everywhere.** `.jmax` runs on the CLI, embeds as a Rust
  library, and runs in the browser compiled to WebAssembly — try it at
  [play.charlot-lang.dev](https://play.charlot-lang.dev).
- **Pure Rust.** No Python, no C++ shared libraries, no LAPACK or BLAS to
  install. One binary.

## Position in the openIE-dev ecosystem

JMax is the scientific-computing surface. See [How it fits](./ecosystem.md) for
the full family — flowG (the substrate), Lux and Joule (sibling languages), and
JouleDB (metered persistence).
