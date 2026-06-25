# jmax examples

Runnable example programs for jmax. Each subdirectory is an independent example
with its own README explaining what it shows and how to run it. Every program
here is verified against the current `jmax` binary.

## Install

```bash
cargo binstall jmax
# or
cargo install jmax
# or download from https://github.com/openIE-dev/jmax/releases
```

No install? Run JMax in your browser at [play.charlot-lang.dev](https://play.charlot-lang.dev).

## Examples

| Example | Shows | Run |
|---|---|---|
| [`hello/`](./hello/) | solve, eigenvalues, determinant | `jmax run examples/hello/hello.jm` |
| [`linear-algebra/`](./linear-algebra/) | `\` solve, Cholesky, determinant | `jmax run examples/linear-algebra/solve.jm` |
| [`signal-processing/`](./signal-processing/) | FFT magnitude spectrum | `jmax run examples/signal-processing/fft.jm` |
| [`statistics/`](./statistics/) | mean/std + OLS regression | `jmax run examples/statistics/stats.jm` |
| [`plotting/`](./plotting/) | `plot` as a statement → SVG | `jmax run examples/plotting/plot.jm` |
| [`symbolic/`](./symbolic/) | exact algebra via `jmax eval` | see README |
| [`optimization/`](./optimization/) | minimize / root / fit (CLI) | see README |

## License

Examples in this directory are licensed under **Apache-2.0** — copy them into
your own projects without license contamination. See [LICENSE](./LICENSE).

This differs from the rest of this repository (binaries under BSL-1.1,
documentation under CC-BY-4.0).
