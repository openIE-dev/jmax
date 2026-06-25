# hello

The minimum useful JMax program: solve a linear system, look at the matrix's
spectrum, and return its determinant.

```bash
jmax run examples/hello/hello.jm
# -> 5.0
```

What this shows:
- Matrices and vectors are first-class values: `[[2.0, 1.0], [1.0, 3.0]]`
- The `\` solve operator: `A \ b`
- Built-in `eig` (eigenvalues) and `det` (determinant) — no imports
