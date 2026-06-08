# linear-algebra

Solve a 1000×1000 random linear system using JMax's built-in `\` operator.

```bash
jmax run solve.jm
jmax run solve.jm --record-energy receipt.json
```

What this shows:
- `randn(rows, cols)` Gaussian random matrix
- The `\` solver (LU under the hood, picked at runtime by op-dispatch)
- `norm()` for the L2 residual

## Backend choice

By default JMax picks the lowest-joule available backend. To force one:

```bash
jmax run solve.jm --backend cpu
jmax run solve.jm --backend metal
jmax run solve.jm --backend wgpu
```
