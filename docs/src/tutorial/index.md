# Tutorial

A hands-on tour of JMax — from a one-line expression to a fitted model with a
plot. Everything here runs in the [browser playground](https://play.charlot-lang.dev)
if you'd rather not install anything yet.

## 1. Evaluate an expression

JMax is math-native: matrices, vectors, and scalars are first-class values, and
the operators mean what they do in mathematics.

```bash
jmax eval "[[2,1],[1,3]] \ [5,10]"
# → [1, 3]      (solves the linear system A x = b)
```

`\` is the solve operator, `*` multiplies matrices, and `'` transposes. No
imports, no setup.

## 2. Write a program

Put a computation in a `.jmax` file. The last expression is the result.

```jmax
fn main() -> float:
  let A = [[2.0, 1.0], [1.0, 3.0]]
  let b = [5.0, 10.0]
  let x = A \ b
  let eigenvalues = eig(A)
  det(A)
```

```bash
jmax run model.jmax
# → 5.0      (det of A; energy-receipted)
```

## 3. Differentiate and optimize — exactly

JMax does both *symbolic* calculus and *automatic* differentiation, and feeds
them into solvers.

```bash
jmax eval "integrate(x^2, x)"           # → x^3/3   (symbolic)
jmax grad "x^2*y + sin(x)" 1.3 0.7      # reverse-mode AD gradient
jmax minimize "(1-x)^2 + 100*(y-x^2)^2" -1.2 1 --newton   # Rosenbrock → (1, 1)
```

## 4. Fit data and plot

Plots are *statements*, not a separate library.

```jmax
fn main():
  let xs = linspace(0.0, 10.0, 100)
  let ys = map(xs, \x -> sin(x) * exp(-0.1 * x))
  plot line xs, ys, title="damped sine", xlabel="t", ylabel="amplitude"
```

```bash
jmax plot signal.jmax     # writes an SVG (and opens it)
```

Curve fitting is one command:

```bash
jmax fit "a*exp(b*x)" data.csv --p0 1,0   # Levenberg–Marquardt, AD Jacobian
```

## 5. Measure the energy

Every workload can report a measured energy receipt in joules — the same
number the browser sandbox models as wall-clock time. Energy is a first-class
observable in JMax, not an afterthought.

## Where to go next

- The full **[built-in function reference](../reference/builtins.md)** — 113 functions.
- The **[`jmax` command line](../reference/cli.md)** — every subcommand.
- The **[cookbook](../cookbook/index.md)** — task-oriented recipes.
- Runnable code in [`examples/`](https://github.com/openIE-dev/jmax/tree/main/examples).
