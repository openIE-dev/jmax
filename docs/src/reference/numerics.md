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

## Matrix decompositions

On top of the core `det`, `inv`, `solve`, `eig`, `svd`, `rank`, and `pinv`:

| Builtin | Result |
|---|---|
| `chol(A)` / `cholesky(A)` | lower Cholesky factor (symmetric positive-definite) |
| `qr(A)` | the upper-triangular R factor of `A = Q R` |
| `cond(A)` | 2-norm condition number |

The library exposes the full factorizations (`qr` returning both `Q` and `R`,
explicit `(L, U, P)`, a one-sided Jacobi SVD, `eigvalsh`, least squares).

## Regression and statistics

| Builtin | Result |
|---|---|
| `lm(X, y)` / `ols(X, y)` | least-squares regression coefficients (intercept first) |
| `corr(A)` | Pearson correlation matrix of the columns of `A` |
| `mean`, `median`, `std`, `var`, `sum`, `min`, `max`, `sort` | over a vector |

The `jmax-stat` library also returns the full regression summary (R squared,
standard errors, t statistics, p values), one-way ANOVA tables, the percentile
bootstrap, and Gaussian kernel density estimation.

## Signal processing

| Builtin | Result |
|---|---|
| `fft(x)` | magnitude spectrum |
| `psd(x)` | power spectral density |
| `hann(n)`, `hamming(n)`, `blackman(n)` | window functions |

The library adds full convolution and correlation, windowed-sinc FIR design (low,
high, band), Butterworth biquads with zero-phase `filtfilt`, and the STFT
spectrogram.

## Calculus, ODEs, and optimization

Adaptive quadrature (`jmax-quad`: Gauss-Kronrod, Gauss-Legendre, Gauss-Hermite),
ODE integration (`jmax-ode`: adaptive RK45 with dense output and event detection,
plus implicit BDF for stiff systems), and unconstrained optimization
(`jmax-optim`: Nelder-Mead, L-BFGS, box-constrained L-BFGS) take a function as
input. They are available through the Rust API today and are being surfaced as
dedicated `jmax` subcommands.

The numerical definite integral has its own command:

```bash
jmax quad "exp(-x^2)" --from -5 --to 5    # 1.7724538509  (sqrt(pi))
jmax quad "x^7" --from 0 --to 1 --method gl --points 4   # 0.125, exact
```

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
