# Symbolic math

JMax does exact computer algebra. Free-variable symbolic work runs through
`jmax eval` (verified output shown):

```bash
jmax eval "integrate(x^2, x)"      # -> 1/3*x^3
jmax eval "expand((x + 1)^2)"      # -> 1 + x^2 + 2*x
```

Identities can be machine-checked:

```bash
jmax verify "(x+1)^2 == x^2 + 2*x + 1"   # CAS self-check + emitted Lean 4 theorem
```

See the [symbolic reference](https://openie-dev.github.io/jmax/reference/builtins.html#symbolic-math).
