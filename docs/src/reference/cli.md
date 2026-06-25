# The `jmax` command line

One binary spans the whole scientific-computing surface — symbolic math,
automatic differentiation, optimization, differential equations, linear
algebra, signal processing, statistics, dataframes, and units. Every command
below is real and runs today. This page mirrors
[api.charlot-lang.dev](https://api.charlot-lang.dev).

## Evaluate & Run

### `jmax eval "<expr>"`

Evaluate an expression — scalars, matrices, vectors, symbolic, or composed numeric functions.

```text
jmax eval "[[1,2],[3,4]] * [5,6]"   → [17, 39]
```

### `jmax run file.jmax`

Run a JMax program and print its result.

```text
jmax run fit_model.jmax
```

### `jmax plot file.jmax`

Run a program and open its plots as SVG.

```text
jmax plot dashboard.jmax
```

### `jmax emit <target> file`

Lower a function to ONNX, StableHLO, WGSL, Triton, or MLIR-linalg.

```text
jmax emit wgsl kernel.jmax
```

## Symbolic & Verification

### `jmax eval "expand((x+1)^2)"`

Computer-algebra: expand, simplify, differentiate, integrate, solve — exact, canonical.

```text
→ 1 + x^2 + 2*x
```

### `jmax eval "integrate(x^2, x)"`

Rule-based symbolic integration; the inverse of differentiation.

```text
→ x^3/3
```

### `jmax eval "solve(x^2-1, x)"`

Solve polynomial equations exactly.

```text
→ x = 1, x = -1
```

### `jmax verify "<lhs> == <rhs>"`

Prove an identity two ways: a sound CAS self-check, and an emitted Lean 4 theorem.

```text
jmax verify "(x+1)^2 == x^2 + 2*x + 1"
```

## Autodiff

### `jmax grad "<f>" <point...>`

Gradient by reverse-mode automatic differentiation of the computation graph.

```text
jmax grad "x^2*y + sin(x)" 1.3 0.7
```

### `jmax hessian "<f>" <point...>`

Full Hessian via second-order (forward-over-reverse) AD.

```text
jmax hessian "x^2*y + sin(x)" 1.3 0.7
```

## Optimization

### `jmax minimize "<f>" <x0...>`

Unconstrained minimization (gradient descent; --newton for the AD-Hessian Newton method).

```text
jmax minimize "(1-x)^2 + 100*(y-x^2)^2" -1.2 1 --newton
```

### `jmax root "<f>" <x0>`

Find a root by Newton's method (derivative via AD).

```text
jmax root "cos(x) - x" 0.5   → 0.7390851
```

### `jmax fit "<model>" data.csv`

Levenberg-Marquardt curve fitting; Jacobian from AD.

```text
jmax fit "a*exp(b*x)" data.csv --p0 1,0
```

### `jmax lp file` · `jmax qp file [--ineq]`

Linear programs (two-phase simplex) and quadratic programs (KKT / active-set).

```text
jmax lp problem.txt   → max cᵀx s.t. Ax ≤ b
```

## Differential Equations

### `jmax ode "<dy/dt>" y0`

Integrate an ODE — adaptive Dormand-Prince RK45, or --stiff for an implicit solver with an AD Jacobian.

```text
jmax ode "1000*(cos(t) - y)" 2 --tf 1 --stiff
```

## Linear Algebra

### `jmax eig file` · `jmax svd file`

Eigenvalues (symmetric, Jacobi) and singular values.

```text
jmax eig sym.txt   → 3, 1
```

### `jmax det file` · `jmax rank file`

Determinant (LU) and numerical rank (SVD).

```text
jmax det m.txt   → 10
```

### `jmax linsolve A b` · `jmax pinv file`

Solve A·x = b, and the Moore-Penrose pseudoinverse.

```text
jmax linsolve A.txt b.txt   → x
```

## Signal Processing

### `jmax fft signal.txt`

Magnitude spectrum via radix-2 FFT.

```text
jmax fft sig.txt   → spectral peak at bin k
```

### `jmax spectrogram signal`

Short-time Fourier transform — per-frame magnitude spectra.

```text
jmax spectrogram sig.txt --frame 16 --hop 8
```

### `jmax biquad signal`

Apply an IIR biquad (RBJ low/high-pass) filter.

```text
jmax biquad sig.txt --kind lowpass --fc 0.1
```

## Statistics

### `jmax stats file`

Summary statistics — n, mean, std, min, median, max.

```text
jmax stats data.txt
```

### `jmax sample <dist> <params>`

Deterministic sampling from Normal / Uniform / Exponential.

```text
jmax sample normal 0 1 -n 5 --seed 42
```

### `jmax ttest a b` · `jmax wilcoxon a b`

Welch two-sample t-test; Wilcoxon signed-rank (paired, nonparametric).

```text
jmax ttest a.txt b.txt   → t, p
```

### `jmax anova g1 g2 …` · `jmax friedman m`

One-way ANOVA, Friedman repeated-measures, Levene, Mann-Whitney, KS.

```text
jmax anova g1.txt g2.txt g3.txt   → F, p
```

## Dataframes

### `jmax df file.csv`

Inspect a CSV: shape, inferred types, head, per-column describe.

```text
jmax df cities.csv
```

### `jmax df file --groupby c --value v`

Group-by aggregation (mean / sum / count / min / max).

```text
jmax df sales.csv --groupby city --value revenue --agg sum
```

### `jmax df file --to-parquet out`

Read CSV or Parquet; write real Parquet (validated against Apache Arrow).

```text
jmax df data.csv --to-parquet data.parquet
```

## Units

### `jmax convert <v> <from> <to>`

Dimensional unit conversion with compound units and metric prefixes.

```text
jmax convert 60 km/hour m/s   → 16.667 m/s
```

