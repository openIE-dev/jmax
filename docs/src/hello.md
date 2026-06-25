# Hello, JMax

The smallest useful program: solve a linear system, look at the matrix's
spectrum, and return its determinant.

```jmax
fn main() -> float:
  let A = [[2.0, 1.0], [1.0, 3.0]]
  let b = [5.0, 10.0]
  let x = A \ b            # solve A x = b  → [1, 3]
  let eigenvalues = eig(A) # symmetric eigenvalues
  det(A)                   # the program's result → 5.0
```

Run it:

```bash
jmax run hello.jmax
# → 5.0
```

Or evaluate an expression directly, no file:

```bash
jmax eval "det([[2,1],[1,3]])"   # → 5
```

Or open a REPL:

```bash
jmax repl
```

No imports, no project scaffold, no numerics package to install — `A \ b` and
`eig` and `det` are part of the language.

**Prefer not to install?** The same program runs in your browser at
[play.charlot-lang.dev](https://play.charlot-lang.dev).

For the full walkthrough — calculus, optimization, fitting, and plotting — see
[the tutorial](./tutorial/index.md). The complete example sources are in
[`examples/`](https://github.com/openIE-dev/jmax/tree/main/examples).
