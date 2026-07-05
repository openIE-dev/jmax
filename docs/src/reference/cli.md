# The `jmax` command line

One binary spans the whole scientific-computing surface — symbolic math,
automatic differentiation, optimization, differential equations, linear
algebra, signal processing, statistics, dataframes, and units. Every command
below is real and runs today. This page mirrors
[api.charlot-lang.dev](https://api.charlot-lang.dev).

## All commands

`jmax` exposes **83 subcommands** spanning symbolic math, numerics, simulation, statistics, and visualization — the whole surface of a MATLAB/NumPy/SciPy-class tool in one binary. Run `jmax <command> --help` for the full options of any one.


### Evaluate & run

| Command | What it does |
|---|---|
| `jmax run` | Run a JMax source file |
| `jmax eval` | Evaluate a single expression |
| `jmax plot` | Run a file and export plots to SVG Plot expression(s) over [from, to] (with --from/--to), or render the plot statements in a source file |
| `jmax export` | Export data/plots to various formats |
| `jmax emit` | Emit the compiled flowG graph to a target IR (onnx|stablehlo|wgsl|triton|mlir-linalg) |
| `jmax ingest` | Ingest a foreign model format into a flowG ProgramGraph JSON (gguf|fx|onnx) |

### Symbolic & verify

| Command | What it does |
|---|---|
| `jmax verify` | Verify a symbolic identity `lhs == rhs` (CAS self-check + Lean proof) |
| `jmax dsolve` | Solve an ODE symbolically: y' = <expr> (first order), or a y''+b y'+c y=0 |

### Calculus & autodiff

| Command | What it does |
|---|---|
| `jmax grad` | Gradient of an expression at a point, by reverse-mode autodiff. Point values bind to the variables in alphabetical order |
| `jmax hessian` | Hessian of an expression at a point, via second-order AD |
| `jmax quad` | Numerically integrate <expr>(var) over [from, to] (adaptive Gauss-Kronrod) |
| `jmax quad2` | Double integral ∫∫ f(x,y) over a rectangle, by tensored adaptive Gauss-Kronrod (SciPy dblquad) |
| `jmax quad3` | Triple integral ∫∫∫ f(x,y,z) over a box, by tensored adaptive Gauss-Kronrod (SciPy tplquad) |

### Roots & nonlinear systems

| Command | What it does |
|---|---|
| `jmax root` | Find a root of a single-variable expression near x0 (Newton + AD) |
| `jmax fsolve` | Solve a general nonlinear system (each eqn = 0, ';'-separated) by damped Newton — SciPy's fsolve. Handles sin/exp/etc; needs a square system |
| `jmax solve-system` | Solve a polynomial system (each eqn = 0, ';'-separated) via Gröbner bases |

### Optimization

| Command | What it does |
|---|---|
| `jmax minimize` | Minimize an expression from a starting point (gradient descent + AD). Start values bind to the variables in alphabetical order |
| `jmax lsq` | Nonlinear least squares: minimize ½Σrᵢ² over arbitrary residual expressions (';'-separated) — SciPy least_squares / MATLAB lsqnonlin. Handles overdetermined systems |
| `jmax fit` | Fit a model to (x, y) data by Levenberg-Marquardt (Jacobian via AD) |
| `jmax lp` | Solve a linear program: maximize cᵀx s.t. Ax ≤ b, x ≥ 0. File: first row = objective c; each later row = "a₁ … aₙ b" |
| `jmax qp` | Solve a QP: min ½xᵀGx + aᵀx s.t. Cx = d (or Cx ≤ d with --ineq). File has `G`/`a`/`C`/`d` section headers, each followed by number rows |

### ODEs, BVPs, SDEs

| Command | What it does |
|---|---|
| `jmax ode` | Integrate dy/dt = <expr>(t, y) from t0 to tf (RK45, or --stiff) |
| `jmax bvp` | Solve a two-point boundary value problem y''=f(x,y,yp) with y(a),y(b) fixed, by shooting (RK45+Newton) — MATLAB bvp4c / SciPy solve_bvp |
| `jmax sde` | Integrate a stochastic ODE dX = drift dt + diffusion dW over many paths |

### Linear algebra — factorization & solve

| Command | What it does |
|---|---|
| `jmax linsolve` | Solve A·x = b. A from matrix file, b from vector file |
| `jmax lusolve` | Solve A·x = b by SPARSE DIRECT factorization: LU, or LDLᵀ/Cholesky with --spd. Exact, unlike the iterative solvers |
| `jmax svd` | Singular values of a matrix (file: rows of numbers) |
| `jmax det` | Determinant of a square matrix (file: rows of numbers) |
| `jmax rank` | Numerical rank of a matrix file (via SVD) |
| `jmax rrqr` | Rank-revealing column-pivoted QR (Businger–Golub): numerical rank + the R diagonal + column permutation |
| `jmax pinv` | Moore-Penrose pseudoinverse of a matrix file |
| `jmax expm` | Matrix exponential e^(A·t) — the state-transition matrix of ẋ=Ax (scaling-and-squaring + Padé). NOT the entrywise exp |
| `jmax sqrtm` | Principal matrix square root √A (X·X = A), by the Denman–Beavers iteration |
| `jmax logm` | Principal matrix logarithm log A (the inverse of expm), by inverse scaling-and-squaring |

### Linear algebra — eigenvalues

| Command | What it does |
|---|---|
| `jmax eig` | Eigenvalues of a symmetric matrix (file: rows of numbers) |
| `jmax eigvals` | All eigenvalues (real + complex) of a GENERAL square matrix, by Hessenberg + Francis QR — LAPACK dgeev-class |
| `jmax eigs` | Extreme eigenvalues of a large SYMMETRIC SPARSE matrix by Lanczos (matvec-only, no factorization) — SciPy eigsh / ARPACK |
| `jmax geneig` | Symmetric-definite generalized eigenvalues K x = λ M x (modal analysis; K symmetric, M SPD) |

### Matrices & tensors — factorization

| Command | What it does |
|---|---|
| `jmax nmf` | Non-negative matrix factorization A ≈ W H, W,H ≥ 0 (interpretable parts; topic models / unmixing). scikit-learn NMF |
| `jmax lowrank` | Best rank-k approximation of a matrix by truncated SVD (Eckart–Young); --report shows energy + compression |
| `jmax einsum` | Einstein summation over matrix operands (NumPy einsum): matmul "ij,jk->ik", trace "ii->", transpose "ij->ji", etc |

### Interpolation & spectral

| Command | What it does |
|---|---|
| `jmax interp` | Interpolate (x, y) samples (spline/linear/pchip/nearest) and plot the curve |
| `jmax cheb` | Chebyshev spectral methods: approximate | diff | integrate an expression to spectral accuracy (chebfun-style) |

### Signal processing

| Command | What it does |
|---|---|
| `jmax fft` | Magnitude spectrum (FFT) of a real signal read from a file |
| `jmax spectrogram` | STFT spectrogram of a real signal: per-frame magnitude spectra |
| `jmax biquad` | Apply a biquad IIR filter to a signal |
| `jmax wavelet` | Discrete wavelet transform: multiresolution energy decomposition or wavelet denoising (SciPy pywt / MATLAB Wavelet Toolbox) |

### Statistics & data

| Command | What it does |
|---|---|
| `jmax stats` | Summary statistics of a data sample read from a file |
| `jmax sample` | Sample from a distribution, e.g. `sample normal 0 1 -n 5 --seed 42` |
| `jmax ttest` | Welch two-sample t-test on two data files |
| `jmax wilcoxon` | Wilcoxon signed-rank test on two paired data files |
| `jmax anova` | One-way ANOVA across groups — one data file per group |
| `jmax friedman` | Friedman test from a matrix file (rows = blocks, columns = treatments) |
| `jmax levene` | Levene's test for equality of variances — one data file per group |
| `jmax df` | Inspect a CSV dataframe (shape, head, describe), or group-by aggregate |
| `jmax forecast` | Time-series forecasting: fit AR / ARIMA / Holt-Winters and forecast ahead |
| `jmax kalman` | Kalman filter + RTS smoother: 1-D constant-velocity position tracking from noisy measurements |

### Simulation — FEM / CFD (2-D & 3-D)

| Command | What it does |
|---|---|
| `jmax heat` | Steady-state heat conduction (FEM) on a rectangle; writes a field SVG. Fix edge temperatures with --left/--right/--top/--bottom (omit = insulated) |
| `jmax stress` | 2-D cantilever (linear elasticity, FEM): clamped left edge, load on the right edge. Reports tip deflection + peak von Mises; writes a stress SVG |
| `jmax modal` | Modal analysis (FEM): natural frequencies + mode shape of a left-clamped plate. Prints the lowest frequencies; plots the chosen mode |
| `jmax transient` | Transient heat (FEM): a hot square cooling with cold edges, integrated implicitly (Crank–Nicolson). Prints the cooling curve; plots the final field |
| `jmax inverse` | Inverse design (differentiable FEM): recover a hidden conductivity inclusion from observed field data via adjoint-gradient descent |
| `jmax heat3d` | 3-D steady heat in a solid box (tetrahedral FEM): hot x=0 face, cold x=length face. Plots the boundary surface temperature (isometric) |
| `jmax stress3d` | 3-D cantilever (solid tetrahedral FEM): clamped x=0 face, downward load on the end face. Reports tip deflection + peak von Mises; plots surface |
| `jmax modal3d` | 3-D modal analysis (tetrahedral FEM): natural frequencies + mode shape of a left-clamped solid. Prints frequencies; plots the deformed mode |
| `jmax stokes` | Incompressible Stokes flow: a lid-driven cavity (top wall slides). Plots the speed field with velocity arrows showing the recirculating vortex |
| `jmax unsteady` | Transient (time-stepped) incompressible Navier–Stokes: a lid-driven cavity spinning up from rest. Prints a kinetic-energy history and plots the final velocity field |
| `jmax stokes3d` | 3-D incompressible Stokes flow in a box "pipe": top face slides in +x, other walls no-slip. Plots the boundary-surface speed field (isometric) |
| `jmax inverse-design` | Multiple-excitation inverse design (differentiable FEM): recover a hidden conductivity inclusion using several boundary excitation patterns at once. Plots true vs recovered conductivity |
| `jmax advect` | Scalar advection–diffusion boundary layer: solved with SUPG vs plain Galerkin to show SUPG removes the high-Péclet oscillations. Writes the SUPG field SVG plus a `-galerkin` companion |
| `jmax thermo` | Thermo-mechanical coupling (multiphysics): steady heat across a clamped plate, then the thermal stress from constrained expansion. von Mises plot |
| `jmax topopt` | Topology optimization (SIMP + Optimality Criteria): find the stiffest material layout of a cantilever for a given volume budget. Renders the optimized density field |
| `jmax topopt3d` | 3-D topology optimization (SIMP + OC on tetrahedra): the stiffest solid layout of a cantilever for a volume budget. Renders the optimized solid |
| `jmax energy` | Energy receipts for sparse-Poisson solves: a 3-D Laplacian scaling study printing exact FLOPs and estimated joules for each ILU(0)-CG solve |
| `jmax bench` | Benchmark the sparse FEM solver: a 3-D Poisson scaling study reporting DOFs, CG iterations, wall time, and throughput (rayon-parallel SpMV) |
| `jmax lbm` | Lattice-Boltzmann CFD (D2Q9 BGK): lid-driven cavity or Poiseuille channel |

### Machine learning for operators

| Command | What it does |
|---|---|
| `jmax fno` | Fourier Neural Operator demo: learn an operator (deriv/integrate/smooth) |

### Visualization

| Command | What it does |
|---|---|
| `jmax chart` | Render a publication-quality chart (SVG) from data. kind: line | scatter | bar | histogram | density | heatmap | box |
| `jmax project` | Project an n-D data matrix down to 2-D (PCA or random) for plotting. Emits `x y` rows — pipe into `jmax chart scatter` |
| `jmax splom` | SPLOM-of-projections: emit an interactive bundle with one linked, brushable scatter panel per projection of the same n-D rows |
| `jmax animate` | Animate a transition between two datasets (state-transition tween). Writes `frame_NNN.svg` you can encode to GIF/MP4, or `--interactive` for an auto-playing WebGPU bundle |

### Units

| Command | What it does |
|---|---|
| `jmax convert` | Convert a value between units, e.g. `convert 60 km/hour m/s` |


The sections below give worked examples for the most common commands.

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

