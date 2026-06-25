# Optimization

Gradient-based solvers with derivatives from automatic differentiation —
driven from the command line (verified output shown):

```bash
jmax minimize "(1-x)^2 + 100*(y-x^2)^2" -1.2 1 --newton   # Rosenbrock -> (1, 1)
jmax root "cos(x) - x" 0.5                                 # -> 0.7390851
jmax fit "a*exp(b*x)" data.csv --p0 1,0                    # Levenberg-Marquardt
```

See the [optimization reference](https://openie-dev.github.io/jmax/reference/builtins.html#optimization).
