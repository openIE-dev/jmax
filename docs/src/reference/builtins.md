# Built-in functions

Every function below is built into the `jmax` binary — no packages to
install. They run identically in the [browser playground](https://play.charlot-lang.dev),
the CLI, and the Rust library. This page mirrors
[docs.charlot-lang.dev](https://docs.charlot-lang.dev).

## Linear Algebra

| Signature | Description |
|---|---|
| `A * B` | Matrix multiply. Use * operator on matrices. |
| `A \ b` | Solve linear system Ax = b. NxN via LU decomposition. |
| `eig(A)` | Eigenvalues of a symmetric matrix via QR algorithm. |
| `svd(A)` | Singular values via eigendecomposition of A'A. |
| `qr(A)` | QR decomposition via Gram-Schmidt. |
| `cholesky(A)` | Cholesky decomposition. A must be symmetric positive definite. |
| `inv(A)` | Matrix inverse via Gauss-Jordan elimination. |
| `det(A)` | Determinant via LU decomposition. |
| `trace(A)` | Sum of diagonal elements. |
| `rank(A)` | Matrix rank via SVD. |
| `A'` | Matrix transpose. Use postfix apostrophe. |
| `eye(n)` | Identity matrix. |
| `zeros(m, n)` | Zero matrix. |
| `ones(m, n)` | All-ones matrix. |
| `linspace(start, end, n)` | N evenly spaced values. |

## Statistics

| Signature | Description |
|---|---|
| `mean(data)` | Arithmetic mean. |
| `std(data)` | Standard deviation (sample). |
| `var(data)` | Variance (sample). |
| `median(data)` | Median value. |
| `percentile(data, p)` | P-th percentile (0-1). |
| `correlation(x, y)` | Pearson correlation coefficient. |
| `covariance(x, y)` | Sample covariance. |
| `t_test(a, b)` | Welch's two-sample t-test. Returns p-value. |
| `chi_squared(observed, expected)` | Chi-squared test statistic. |
| `linear_regression(x, y)` | OLS. Returns [intercept, slope, r_squared]. |
| `normal_cdf(x)` | Standard normal CDF. |
| `normal_pdf(x)` | Standard normal PDF. |

## Signal Processing

| Signature | Description |
|---|---|
| `fft(signal)` | Fast Fourier Transform (Cooley-Tukey). Returns magnitude spectrum. |
| `ifft(spectrum)` | Inverse FFT. |
| `convolve(a, b)` | Linear convolution. |
| `fir_filter(signal, coeffs)` | FIR filter. |
| `spectrogram(signal, win, hop)` | STFT magnitude spectrogram. |
| `hann(n)` | Hann window of length n. |
| `hamming(n)` | Hamming window of length n. |
| `blackman(n)` | Blackman window of length n. |

## Machine Learning

| Signature | Description |
|---|---|
| `relu(x)` | Rectified linear unit. max(0, x). |
| `sigmoid(x)` | 1 / (1 + exp(-x)). |
| `tanh_act(x)` | Hyperbolic tangent activation. |
| `gelu(x)` | Gaussian error linear unit. |
| `swish(x)` | x * sigmoid(x). |
| `softmax(logits)` | Softmax over a vector. Numerically stable. |
| `cross_entropy(pred, target)` | Cross-entropy loss. |
| `mse_loss(pred, target)` | Mean squared error loss. |
| `accuracy(pred, target)` | Classification accuracy. |

## Symbolic Math

| Signature | Description |
|---|---|
| `sin(x)` | Sine. |
| `cos(x)` | Cosine. |
| `tan(x)` | Tangent. |
| `asin(x)` | Arcsine. |
| `acos(x)` | Arccosine. |
| `exp(x)` | e^x. |
| `log(x)` | Natural logarithm. |
| `log2(x)` | Base-2 logarithm. |
| `log10(x)` | Base-10 logarithm. |
| `sqrt(x)` | Square root. |
| `abs(x)` | Absolute value. |
| `floor(x)` | Floor. |
| `ceil(x)` | Ceiling. |
| `round(x)` | Round to nearest integer. |

## Finance

| Signature | Description |
|---|---|
| `black_scholes_call(S, K)` | Black-Scholes call option price. T=1yr, r=5%, sigma=20%. |
| `black_scholes_put(S, K)` | Black-Scholes put option price. |
| `sma(data, window)` | Simple moving average. |
| `ema(data, span)` | Exponential moving average. |
| `sharpe(returns, risk_free)` | Sharpe ratio. |
| `max_drawdown(prices)` | Maximum drawdown. |
| `npv(rate, cash_flows)` | Net present value. |
| `irr(cash_flows)` | Internal rate of return. |

## Bioinformatics

| Signature | Description |
|---|---|
| `smith_waterman(seq1, seq2)` | Local alignment score. |
| `needleman_wunsch(seq1, seq2)` | Global alignment score. |
| `gc_content(seq)` | GC content ratio (0-1). |
| `reverse_complement(seq)` | DNA reverse complement. |
| `translate_dna(seq)` | Translate DNA to protein. |

## Data Analysis

| Signature | Description |
|---|---|
| `sort(data)` | Sort a vector ascending. |
| `unique(data)` | Remove duplicates. |
| `cumsum(data)` | Cumulative sum. |
| `diff(data)` | First differences. |
| `len(data)` | Length of a vector or array. |

## Visualization

| Signature | Description |
|---|---|
| `plot line data` | Line chart. |
| `plot scatter data` | Scatter plot. |
| `plot bar data` | Bar chart. |
| `plot histogram data` | Histogram with automatic binning. |
| `plot heatmap matrix` | Heatmap with Viridis colormap. |
| `plot surface matrix` | 3D surface plot. |
| `plot contour matrix` | Contour plot with marching squares. |
| `plot boxplot data` | Box-and-whisker plot. |
| `plot violin data` | Violin plot with KDE. |
| `plot radar data` | Radar/spider chart. |

## Calculus & Autodiff

| Signature | Description |
|---|---|
| `d/dx(f)` | Exact symbolic differentiation. |
| `integrate(f, x)` | Rule-based symbolic integration (linearity, power rule, standard functions). |
| `expand(e)` | Distribute products and expand integer powers of sums. |
| `simplify(e)` | Canonical simplification via equality-saturation (e-graph). |
| `solve(expr, x)` | Solve polynomial equations exactly (linear, quadratic). |
| `grad(f, point)` | Gradient by reverse-mode automatic differentiation. |
| `hessian(f, point)` | Full Hessian by second-order AD. |

## Optimization

| Signature | Description |
|---|---|
| `minimize(f, x0)` | Unconstrained minimization — gradient descent or Newton (AD Hessian). |
| `root(f, x0)` | Find a root by Newton's method (AD derivative). |
| `fit(model, data)` | Levenberg-Marquardt nonlinear least-squares curve fitting. |
| `lp(c, A, b)` | Linear program by two-phase simplex (Bland anti-cycling). |
| `qp(G, a, C, d)` | Quadratic program — equality (KKT) or inequality (active-set). |

## Differential Equations

| Signature | Description |
|---|---|
| `ode("dy/dt", y0)` | Adaptive Dormand-Prince RK45 integrator. |
| `ode(…, --stiff)` | Implicit backward-Euler for stiff systems; Jacobian via AD. |

## Hypothesis Tests

| Signature | Description |
|---|---|
| `anova(g1, g2, …)` | One-way ANOVA F-test for equal group means. |
| `wilcoxon(a, b)` | Wilcoxon signed-rank test (paired, nonparametric). |
| `mann_whitney(a, b)` | Mann-Whitney U test (independent samples). |
| `levene(g1, g2, …)` | Levene/Brown-Forsythe test for equal variances. |
| `friedman(blocks)` | Friedman repeated-measures test. |
| `ks_two_sample(a, b)` | Two-sample Kolmogorov-Smirnov test. |

## Dataframes

| Signature | Description |
|---|---|
| `df file.csv` | Typed columnar dataframe from CSV (with type inference). |
| `groupby(key, value, op)` | Group-by aggregation: sum, mean, count, min, max. |
| `pivot(index, cols, value)` | Pivot table. |
| `join(other, on)` | Inner, left, or outer join. |
| `--to-parquet out` | Write real Parquet (validated against Apache Arrow). |

## Units

| Signature | Description |
|---|---|
| `convert(v, from, to)` | Dimensional unit conversion with compound units and metric prefixes. |
| `Quantity{value, dim}` | Dimension-tracked arithmetic (SI base dimensions). |

