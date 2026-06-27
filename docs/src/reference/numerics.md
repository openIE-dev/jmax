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
