# Cookbook

Task-oriented recipes. Each one is a few lines you can paste into the
[playground](https://play.charlot-lang.dev) or a `.jmax` file.

## Solve a linear system

```jmax
let A = [[2.0, 1.0], [1.0, 3.0]]
let b = [5.0, 10.0]
A \ b            # → [1, 3]
```

## Eigenvalues, determinant, inverse

```jmax
let A = [[2.0, 1.0], [1.0, 3.0]]
eig(A)           # eigenvalues (symmetric QR)
det(A)           # determinant (LU)
inv(A)           # inverse (Gauss–Jordan)
```

## Summary statistics

```jmax
let x = [4.0, 8.0, 15.0, 16.0, 23.0, 42.0]
mean(x); std(x); median(x); percentile(x, 0.9)
```

## Fit a line

```jmax
let xs = [1.0, 2.0, 3.0, 4.0, 5.0]
let ys = [2.1, 3.9, 6.2, 7.8, 10.1]
linear_regression(xs, ys)    # → [intercept, slope, r_squared]
```

## FFT of a signal

```jmax
let t = linspace(0.0, 1.0, 256)
let sig = map(t, \x -> sin(2.0 * 3.14159 * 5.0 * x))
fft(sig)          # magnitude spectrum — peak at 5 Hz
```

## Symbolic calculus

```jmax
diff(sin(x) * x^2, x)      # exact derivative
integrate(x^2, x)          # → x^3/3
simplify((x + 1)^2 - x^2)  # → 1 + 2*x
solve(x^2 - 1, x)          # → x = 1, x = -1
```

## Gradient-based optimization

```jmax
# Minimize the Rosenbrock function with the AD-Hessian Newton method
minimize("(1-x)^2 + 100*(y-x^2)^2", [-1.2, 1.0], --newton)   # → (1, 1)
```

## Black–Scholes option price

```jmax
black_scholes_call(100.0, 105.0)   # S=100, K=105, T=1yr, r=5%, σ=20%
```

## A dataframe group-by (CLI)

```bash
jmax df sales.csv --groupby city --value revenue --agg sum
```

## Plot a chart

```jmax
let cats = ["NY", "LA", "SF", "CHI"]
let vals = [120.0, 200.0, 90.0, 150.0]
plot bar vals, title="revenue"
```

JMax plots `line`, `scatter`, `bar`, `histogram`, `density`, `heatmap`,
`boxplot`, `violin`, `contour`, `polar`, `stem`, 3-D `scatter`, and 3-D
`surface` — see the [visualization reference](../reference/builtins.md#visualization).

## Convert units

```bash
jmax convert 60 km/hour m/s     # → 16.667 m/s
```

## Verify an identity

```bash
jmax verify "(x+1)^2 == x^2 + 2*x + 1"   # CAS self-check + emitted Lean 4 theorem
```
