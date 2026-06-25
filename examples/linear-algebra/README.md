# linear-algebra

Solve a linear system with the built-in `\` operator, verify the result, and
take a Cholesky factorization — pure Rust, no LAPACK to install.

```bash
jmax run examples/linear-algebra/solve.jm
# -> 11.0   (det of A)
```

What this shows:
- The `\` solver (LU under the hood, dispatched at runtime)
- Matrix multiply `A * x` to check the residual
- `cholesky(A)` for a symmetric positive-definite matrix, and `det(A)`

The full linear-algebra surface (`eig`, `svd`, `qr`, `inv`, `rank`, `pinv`, …)
is in the [reference](https://openie-dev.github.io/jmax/reference/builtins.html#linear-algebra).
