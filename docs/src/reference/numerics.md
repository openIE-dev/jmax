# Numerics

Beyond core linear algebra, JMax carries a deep numerical library: special
functions, interpolation, polynomials, matrix decompositions, regression, and
signal processing. The pieces that take and return numbers, vectors, or matrices
are callable directly from `jmax eval` and `jmax run`; all of them are pure Rust,
with no BLAS, LAPACK, or SciPy underneath, and each is verified against analytic
or tabulated results.

```bash
jmax eval "gamma(0.5)"              # 1.7724538509  (sqrt(pi))
jmax eval "roots([-6, 11, -6, 1])" # the roots of x^3 - 6x^2 + 11x - 6: 1, 2, 3
```

## Special functions

Applied elementwise: a scalar maps to a scalar, a vector maps componentwise.

| Builtin | Function |
|---|---|
| `gamma(x)` | gamma function |
| `lgamma(x)` / `ln_gamma(x)` | log gamma (finite where gamma overflows) |
| `digamma(x)` | digamma (derivative of log gamma) |
| `erf(x)` / `erfc(x)` | error function and its complement |
| `beta(a, b)` | beta function |
| `bessel_j0`, `bessel_j1` | Bessel functions of the first kind |
| `bessel_y0` | Bessel function of the second kind |
| `bessel_i0`, `bessel_k0` | modified Bessel functions |

The Rust library additionally provides higher integer orders (`bessel_jn`,
`bessel_yn`, `bessel_kn`), the inverse error function, the regularized incomplete
gamma and beta integrals (the kernels behind the statistical distributions), and
the complete elliptic integrals.

## Polynomials

Coefficients are in ascending powers: `[c0, c1, c2]` is `c0 + c1 x + c2 x^2`.

| Builtin | Result |
|---|---|
| `polyval(coeffs, x)` | evaluate at a scalar or vector `x` |
| `polyfit(xs, ys, degree)` | least-squares polynomial coefficients |
| `roots(coeffs)` | all roots, as an N by 2 matrix of `[real, imag]` |

```bash
# Fit a quadratic to noisy samples, then evaluate the fit.
jmax eval "polyval(polyfit([0,1,2,3], [1,2,5,10], 2), 1.5)"
```

`roots` uses the Aberth-Ehrlich method and returns real and complex roots
together; a real root has a near-zero imaginary part.

## Interpolation

| Builtin | Scheme |
|---|---|
| `interp1(xs, ys, xq)` | piecewise linear |
| `spline(xs, ys, xq)` | natural cubic spline |

The query `xq` may be a scalar or a vector. The Rust library also offers clamped
splines, monotone PCHIP, 2D bilinear, and radial-basis-function scattered
interpolation.

The `jmax interp` command interpolates `(x, y)` samples from a data file and
plots the sample markers with the interpolant curve overlaid:

```bash
jmax interp data.dat --method spline --at 2.5 --plot interp.svg
jmax interp data.dat --method pchip --plot pchip.svg   # monotone, no overshoot
```

Methods: `spline` (natural cubic, the default), `linear`, `pchip`, `nearest`.

For a *known* function (not scattered samples), `jmax cheb` uses Chebyshev
spectral methods: interpolation on Chebyshev–Gauss–Lobatto nodes converges
*spectrally* (faster than any power of 1/n, with no Runge phenomenon), and the
same representation differentiates and integrates to the same accuracy
(chebfun-style):

```bash
jmax cheb approx "exp(x)" -n 20                     # max error ~1e-15 with 20 nodes
jmax cheb diff "sin(x)" --at 1 --exact 0.5403       # spectral derivative
jmax cheb integrate "4/(1+x^2)" --from 0 --to 1 --exact 3.14159265   # Clenshaw–Curtis
jmax cheb roots "sin(3.14159*x)" --from -2.5 --to 2.5   # every zero in the interval
```

`cheb roots` finds *all* the real zeros of a smooth function at once — no
bracketing or starting guesses — as the real eigenvalues of the function's
Chebyshev *colleague matrix* (the chebfun approach), which is what makes it
robust for closely spaced or many roots.

## Matrix decompositions

On top of the core `det`, `inv`, `solve`, `eig`, `svd`, `rank`, and `pinv`:

| Builtin | Result |
|---|---|
| `chol(A)` / `cholesky(A)` | lower Cholesky factor (symmetric positive-definite) |
| `qr(A)` | the upper-triangular R factor of `A = Q R` |
| `cond(A)` | 2-norm condition number |

The library exposes the full factorizations (`qr` returning both `Q` and `R`,
explicit `(L, U, P)`, a one-sided Jacobi SVD, `eigvalsh`, least squares).

`jmax eig` handles the *symmetric* eigenproblem. For a **general** (nonsymmetric)
matrix — whose eigenvalues can be complex — `jmax eigvals` computes the whole
spectrum by Hessenberg reduction + Francis double-shift QR (the LAPACK `dgeev`
approach), reporting each eigenvalue as `re ± im·i`:

```bash
jmax eigvals A.dat        # every eigenvalue of a general square matrix
```

`jmax geneig` solves the symmetric-definite *generalized* eigenproblem
`K x = λ M x` — the natural-frequency / mode-shape problem of structural and
vibration analysis (MATLAB `eig(K,M)` / SciPy `eigh(K,M)`), with `K` symmetric
and `M` symmetric positive-definite:

```bash
jmax geneig K.dat M.dat    # generalized eigenvalues of K x = λ M x
```

`jmax expm` computes the matrix *exponential* `e^{A·t}` — the state-transition
matrix of the linear system `ẋ = A x` (so `x(t) = e^{A t} x(0)`), and the tool
behind continuous-time control and Markov chains. It is not the entrywise
exponential; it uses scaling-and-squaring with a Padé approximant (MATLAB /
SciPy `expm`):

```bash
jmax expm A.dat --t 0.5    # e^(A·0.5)
```

Its companions `jmax sqrtm` and `jmax logm` compute the principal matrix square
root (`X·X = A`, Denman–Beavers) and logarithm (the inverse of `expm`) — the
matrix-function trio behind matrix means and manifold computations:

```bash
jmax sqrtm A.dat     # principal √A
jmax logm  A.dat     # principal log A   (logm(expm(A)) = A)
```

`jmax eig` diagonalizes a matrix densely. For a matrix too large to factor —
where you want only a few eigenvalues at one end of the spectrum — `jmax eigs`
uses Lanczos, which needs only matrix-vector products (SciPy `eigsh` / ARPACK):

```bash
jmax eigs matrix.dat --k 5 --which LA          # 5 largest (algebraic) eigenvalues
jmax eigs matrix.dat --k 3 --which SA          # 3 smallest
jmax eigs laplacian.txt --k 2 --triplets       # sparse input: "row col value" per line
```

`--which` selects the end of the spectrum (`LA` largest, `SA` smallest, `LM`
largest magnitude); `--triplets` reads coordinate form for a genuinely sparse
matrix; `--maxiter` raises the Krylov budget for tightly clustered spectra.

To reach eigenvalues buried in the *interior* of the spectrum — near a
resonance, a design frequency, a bandgap — pass `--sigma`, which runs
shift-invert Lanczos on `(A − σI)⁻¹` and returns the eigenvalues nearest σ
(SciPy `eigsh(sigma=…)` / ARPACK shift-invert):

```bash
jmax eigs matrix.dat --k 3 --sigma 2.0    # the 3 eigenvalues closest to 2.0
```

The iterative solvers approximate; for an *exact* solve of a large sparse
system, `jmax lusolve` factors it directly — sparse LU (Gilbert–Peierls, with
partial pivoting) for a general matrix, or LDLᵀ / sparse Cholesky with `--spd`
for a symmetric positive-definite one:

```bash
jmax lusolve A.dat b.vec              # sparse LU:  A x = b
jmax lusolve K.dat f.vec --spd       # sparse Cholesky (SPD), e.g. an FEM stiffness solve
jmax lusolve big.txt b.vec --triplets   # coordinate-format sparse input
```

### Tensors and einsum

Beyond matrices, JMax has N-dimensional tensors with NumPy broadcasting,
reshaping, axis reductions, and a general `einsum`. On the CLI, `jmax einsum`
runs Einstein summation over matrix operands — matrix multiply, trace,
transpose, and contraction all in one index notation:

```bash
jmax einsum "ij,jk->ik" A.dat B.dat   # matrix multiply
jmax einsum "ii->" A.dat              # trace
jmax einsum "ij->ji" A.dat            # transpose
```

`jmax lowrank` compresses a matrix to its best rank-`k` approximation by
truncated SVD (Eckart–Young); `--report` shows the singular values, the retained
energy, the Frobenius error `‖A − Aₖ‖`, and the storage ratio:

```bash
jmax lowrank data.dat --rank 5 --report    # rank-5 approximation + compression stats
```

Where SVD gives signed orthogonal factors, `jmax nmf` gives *non-negative* ones
— `A ≈ W H` with `W, H ≥ 0` — so the factors read as additive, interpretable
parts (topics, spectral endmembers, source signals; scikit-learn's `NMF`):

```bash
jmax nmf counts.dat --rank 3    # 3 non-negative parts W and their coefficients H
```

The library also carries two N-dimensional tensor factorizations: Tucker /
higher-order SVD (`hosvd`, `tucker_reconstruct`) for orthogonal subspace
compression, and CP / PARAFAC (`cp_als`) for a sum of interpretable rank-1
components.

## Regression and statistics

| Builtin | Result |
|---|---|
| `lm(X, y)` / `ols(X, y)` | least-squares regression coefficients (intercept first) |
| `corr(A)` | Pearson correlation matrix of the columns of `A` |
| `mean`, `median`, `std`, `var`, `sum`, `min`, `max`, `sort` | over a vector |

The `jmax-stat` library also returns the full regression summary (R squared,
standard errors, t statistics, p values), one-way ANOVA tables, the percentile
bootstrap, and Gaussian kernel density estimation.

### Time series

`jmax forecast` fits a classical time-series model to a series and projects it
forward — AR(p) (Yule–Walker), ARIMA(p, d, q), or additive Holt–Winters triple
exponential smoothing:

```bash
jmax forecast series.dat --model arima --p 2 --d 1 --q 0 --steps 12
jmax forecast sales.dat --model hw --season 12 --steps 24   # trend + seasonality
```

### State estimation

`jmax kalman` runs a Kalman filter (and, with `--smooth`, the RTS smoother) over
noisy 1-D position measurements using a constant-velocity model — recovering
position *and* velocity and de-noising the track:

```bash
jmax kalman track.dat --meas-noise 0.4 --proc-noise 1e-3 --smooth --plot k.svg
```

## Signal processing

| Builtin | Result |
|---|---|
| `fft(x)` | magnitude spectrum |
| `psd(x)` | power spectral density |
| `hann(n)`, `hamming(n)`, `blackman(n)` | window functions |

The library adds full convolution and correlation, windowed-sinc FIR design (low,
high, band), Butterworth biquads with zero-phase `filtfilt`, and the STFT
spectrogram.

Where the FFT trades all time information for frequency, the **wavelet**
transform keeps both, localizing a transient in time *and* scale. `jmax wavelet`
runs the orthonormal fast wavelet transform (Haar, `db2`, `db4`) — either a
multiresolution decomposition (energy per scale) or wavelet denoising
(soft-threshold, VisuShrink), the tools of SciPy `pywt`:

```bash
jmax wavelet signal.dat --wavelet db4 --level 4              # energy per scale
jmax wavelet signal.dat --wavelet db4 --denoise --plot d.svg  # denoise + overlay
```

## Calculus, ODEs, and optimization

Adaptive quadrature (`jmax-quad`: Gauss-Kronrod, Gauss-Legendre, Gauss-Hermite),
ODE integration (`jmax-ode`: adaptive RK45 with dense output and event detection,
plus implicit BDF for stiff systems), and unconstrained optimization
(`jmax-optim`: Nelder-Mead, L-BFGS, box-constrained L-BFGS) take a function as
input. They are available through the Rust API today and are being surfaced as
dedicated `jmax` subcommands.

`jmax minimize` also takes `--constraints`: a `;`-separated list of relations
(`<=`, `>=`, `=`) in the objective's variables, solved by the augmented
Lagrangian method (SciPy `minimize(constraints=…)` / MATLAB `fmincon`):

```bash
jmax minimize "x^2 + y^2" 0 0 --constraints "x + y = 1"            # -> (0.5, 0.5)
jmax minimize "(x-2)^2 + (y-1)^2" 0 0 --constraints "x^2 + y^2 <= 1"  # project onto the disk
jmax minimize "x^2+y^2+z^2" 0 0 0 --constraints "x+y+z = 1; x >= 0.6"  # equality + inequality
```

Equalities become `g = 0` and inequalities `g ≤ 0`; the report gives the
solution, the objective, and the worst constraint violation.

The other minimizers are local — they descend to the nearest basin. For a
multimodal objective, `--global` searches a whole box by Differential Evolution
(SciPy `differential_evolution`), then polishes the winner with box-constrained
L-BFGS. It is deterministic given its seed, and needs `--bounds` (one `lo,hi`
per variable, alphabetical):

```bash
# Rastrigin: a lattice of local minima; DE finds the global one at the origin.
jmax minimize "20 + (x^2-10*cos(6.2831853*x)) + (y^2-10*cos(6.2831853*y))" \
    --global --bounds "-5.12,5.12; -5.12,5.12"
```

The numerical definite integral has its own command:

```bash
jmax quad "exp(-x^2)" --from -5 --to 5    # 1.7724538509  (sqrt(pi))
jmax quad "x^7" --from 0 --to 1 --method gl --points 4   # 0.125, exact
```

Multidimensional integrals over a rectangle or box use `jmax quad2` and
`jmax quad3` (tensored adaptive Gauss-Kronrod; SciPy `dblquad`/`tplquad`):

```bash
jmax quad2 "exp(-(x^2+y^2))" --xfrom -3 --xto 3 --yfrom -3 --yto 3   # ≈ π
jmax quad3 "x*y*z"           # ∫∫∫ over the unit cube = 1/8
```

Stochastic differential equations integrate over many sample paths with
`jmax sde` (Euler-Maruyama or Milstein):

```bash
# geometric Brownian motion; mean of X(T) approaches X0 * e^(mu*T)
jmax sde "0.05*y" "0.2*y" --y0 1 --t1 1 --paths 4000 --plot path.svg
```

Ordinary differential equations are solved with `jmax ode`; `--method rosenbrock`
selects an L-stable stiff solver, and `--events "<g>"` reports zero-crossings:

```bash
jmax ode "-1000*(y - cos(t)) - sin(t)" 1 --tf 1 --method rosenbrock   # stiff -> cos(t)
```

Where `jmax ode` marches an initial value forward, `jmax bvp` solves a
*boundary* value problem — `y'' = f(x, y, yp)` on `[a, b]` with the solution
pinned at both ends, `y(a)` and `y(b)`. It shoots: the unknown initial slope is
found by driving the far-boundary residual to zero (adaptive RK45 inside a
Newton solve). The right-hand side is written in `x`, `y`, and `yp` (for `y'`):

```bash
jmax bvp "-y" --a 0 --b 1.5708 --ya 0 --yb 1                       # y''=-y -> sin(x)
jmax bvp "6*x" --a 0 --b 1 --ya 0 --yb 1 --plot cubic.svg          # y''=6x -> x^3
jmax bvp "-sin(y) - 0.1*yp" --a 0 --b 4 --ya 1 --yb -0.3 --slope -0.5  # nonlinear
```

The default method is single shooting (integrate from one end, Newton-solve for
the initial slope). For stiff or strongly nonlinear problems, where forward
integration amplifies the slope error, `--method collocation` instead
discretizes the whole trajectory and solves it at once by 4th-order
Hermite-Simpson collocation — the method behind MATLAB `bvp4c` / SciPy
`solve_bvp`:

```bash
jmax bvp "6*x" --a 0 --b 1 --ya 0 --yb 1 --method collocation --nodes 40
```

Shooting hits linear problems in one Newton step; collocation returns the whole
mesh solution and stays accurate where shooting struggles.

`--method adaptive` goes further: it starts from a coarse mesh, estimates the
error on each interval, and refines where the solution is hardest to resolve —
automatically capturing boundary layers and sharp features a uniform mesh would
miss, and falling back to homotopy continuation when the nonlinear solve stalls:

```bash
jmax bvp "80*sinh(y)" --a 0 --b 1 --ya 0 --yb 1 --method adaptive --nodes 8
# converges by refining ~8 -> ~90 nodes to resolve the boundary layer near x=1
```

It reports the initial and final node counts and the largest remaining interval
error. Mesh refinement is robust from a plain guess; the continuation fallback
widens the basin for hard nonlinear solves but a pathological guess on a
multi-solution problem can still need a better start.

## Machine learning for operators

`jmax fno` demonstrates a Fourier Neural Operator learning a solution *operator*
from example fields (differentiation, integration, or smoothing) rather than a
fixed input/output pair:

```bash
jmax fno --operator deriv --plot fno.svg     # learns differentiation to ~1e-15
jmax fno --operator smooth                   # learns a Gaussian-smoothing operator
```

The spectral convolution (FFT, per-mode complex weights, inverse FFT) and the
operator fit are pure Rust; this is the building block for PDE surrogate models.

## Symbolic algebra

JMax carries a computer algebra system. From `jmax eval`, expressions with free
variables are simplified symbolically, and the calculus functions fold to their
results:

```bash
jmax eval "d/dx (x^3 + sin(x))"            # 3*x^2 + cos(x)
jmax eval "integrate(x*sin(x), x)"         # sin(x) - x*cos(x)   (by parts)
jmax eval "integrate(1/(x^2 - 1), x)"      # partial fractions to logs
jmax eval "expand_trig(sin(2*x))"          # 2*cos(x)*sin(x)
jmax eval "sin(x)^2 + cos(x)^2"            # 1
```

Supported symbolic operations include differentiation, integration (power rule,
the standard table, integration by parts, u-substitution, partial fractions),
Taylor series, limits (continuity, L'Hopital, rational forms at infinity),
equation solving (linear, quadratic, cubic, and linear systems), polynomial
expand/collect, substitution, and trig rewriting.

Systems of polynomial equations are solved with `jmax solve-system`, which builds
a Gröbner basis and back-substitutes for the real solutions (Mathematica-class
solving of nonlinear systems):

```bash
jmax solve-system "x^2 + y^2 - 1; x - y" --vars x,y   # circle meets line: (±0.707, ±0.707)
jmax solve-system "x^2 - y; y - 4" --vars x,y         # (±2, 4)
```

Each `;`-separated expression is read as `= 0`. The solver handles
zero-dimensional systems and reports inconsistent or positive-dimensional ones.

For systems that are *not* polynomial — anything with `sin`, `exp`, `log`, or
ratios — `jmax fsolve` finds a root numerically by damped Newton iteration from
a starting guess, converging to the solution nearest the start. This is the
floating-point counterpart to `solve-system` (SciPy `fsolve` / MATLAB `fsolve`):

```bash
jmax fsolve "x*cos(y) - 1; x*sin(y) - 1" --vars x,y                 # -> (√2, π/4)
jmax fsolve "x^2 + y^2 - 1; y - x" --vars x,y --start -2,-0.5       # the negative root
jmax fsolve "x+y+z-6; x^2+y^2+z^2-14; x*y*z-6" --vars x,y,z --start 0.5,1.5,3.5  # (1,2,3)
```

The system must be square (one equation per variable); a singular Jacobian is
handled by a Levenberg-Marquardt fallback, and `--start` selects which root a
multi-root system converges to.

When there are *more* residuals than unknowns — an overdetermined system, the
usual case in fitting and calibration — `jmax lsq` minimizes the sum of squares
`½Σrᵢ²` by Levenberg-Marquardt instead of demanding an exact root (SciPy
`least_squares` / MATLAB `lsqnonlin`):

```bash
# Least-squares line through 4 non-collinear points -> a=1.1, b=1.1
jmax lsq "a+b*0-1; a+b*1-3; a+b*2-2; a+b*3-5" --vars a,b
# Three circles that nearly meet: the best-fit intersection point
jmax lsq "sqrt(x^2+y^2)-1.4142; sqrt((x-2)^2+y^2)-1.4142; sqrt(x^2+(y-2)^2)-1.4142" --vars x,y --start 0,0
```

Each `;`-separated expression is a residual driven toward zero; the report gives
the fitted values, the residual norm `‖r‖`, and the cost `½‖r‖²`.

Ordinary differential equations are solved symbolically with `jmax dsolve`:

```bash
jmax dsolve "1 - y"          # y' = 1 - y   ->  y = exp(-x)*(C + exp(x))
jmax dsolve --linear2 "1,0,1"  # y'' + y = 0  ->  y = C1*cos(x) + C2*sin(x)
```

The first-order solver handles separable and linear equations; `--linear2`
solves the homogeneous constant-coefficient second-order equation
`a y'' + b y' + c y = 0` through its characteristic roots (real, repeated, and
complex cases).

## Plots

`jmax plot` graphs one or more single-variable expressions over a domain,
overlaying them in one figure:

```bash
jmax plot "sin(x)/x" --from -20 --to 20 --out sinc.svg
jmax plot "sin(x)" "cos(x)" "sin(x)*exp(-x/6)" --from 0 --to 12
jmax plot "bessel_j0(x)" "bessel_j1(x)" --from 0 --to 20
```

The expressions use the same scalar interpreter as the other commands, so the
standard math and special functions are available.

For optimization, `minimize --contour` draws a 2-variable objective as a contour
with the optimizer's descent path overlaid:

```bash
jmax minimize "(1-x)^2 + 100*(y-x^2)^2" -1 1 --method lbfgs --contour rosen.svg
```

Several numerical commands also write a publication-quality SVG with `--plot`:

```bash
jmax quad "exp(-x^2)" --from -3 --to 3 --plot integral.svg   # integrand, area shaded
jmax ode "cos(t)" 0 --tf 7 --plot trajectory.svg             # the solution y(t)
jmax ode "cos(t)" 0 --t0 1 --tf 7 --events "y" --plot ev.svg # trajectory + event markers
jmax fit "a*exp(b*x)" data.dat --p0 1,1 --plot fit.svg       # data points + fitted curve
jmax spectrogram signal.dat --plot spec.svg                  # time x frequency heatmap
jmax df data.csv --plot dist.svg                             # facet of column histograms
jmax df data.csv --groupby region --value sales --plot bar.svg   # grouped bar chart
```

The simulation commands likewise render their fields (`jmax heat --out heat.svg`,
and so on); see the [Simulation](./simulation.md) reference.

### Interactive output

`jmax plot`, `quad`, `ode`, `fit`, and `interp` also accept `--html <dir>`, which
writes a self-contained interactive bundle (an `index.html`, the figure as
`viz.json`, and the WebGPU viewer) instead of a static SVG. The same figure is
rendered on a canvas you can drag to pan, scroll to zoom, shift-drag to brush,
and double-click to reset:

```bash
jmax plot "sin(x)/x" --from -20 --to 20 --html playground
cd playground && python3 -m http.server   # then open the page
```

`jmax df --html` writes a multi-canvas facet of interactive column histograms (or
an interactive bar chart for a `--groupby` result). `jmax spectrogram --html`
writes an interactive viridis heatmap, and `jmax minimize --contour --html` writes
an interactive contour of the objective with the descent path overlaid (the same
`--contour` also writes a static SVG). All support drag-pan, wheel-zoom, and
double-click reset:

```bash
jmax df data.csv --html dist
jmax spectrogram signal.dat --html spec
jmax minimize "(1-x)^2 + 100*(y-x^2)^2" -1 1 --contour rosen.svg --html descent
```
