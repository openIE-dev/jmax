# Simulation

JMax ships a native finite-element simulation stack: mesh generation, assembly,
sparse Krylov solvers, and a family of physics solvers, all pure Rust with no
external BLAS, LAPACK, or PETSc. Every command below solves a real PDE on a
generated mesh, prints diagnostics, and writes a publication-quality SVG.

Every solve also reports an **energy receipt**: the exact floating-point
operation count of the linear solve, plus an estimated energy cost. The FLOP
count is exact; the joules are an estimate at a documented per-FLOP rate. This is
the JoulesPerBit idea made routine, so the cost of a computation is visible next
to its result.

```
energy (est.)     : ≈ 96.8 MFLOP, ~5.8 mJ (est.)
```

## Solid mechanics

| Command | Solves | Render |
|---|---|---|
| `jmax heat` | steady heat conduction, `-div(k grad T) = f` (2D) | temperature field |
| `jmax stress` | linear elasticity, plane stress cantilever (2D) | von Mises stress |
| `jmax modal` | natural frequencies and mode shapes (2D) | deformed mode shape |
| `jmax transient` | time-dependent heat (theta-method) (2D) | cooling field + curve |
| `jmax heat3d` | steady heat in a solid box (3D tetrahedra) | isometric surface field |
| `jmax stress3d` | linear elasticity cantilever (3D tetrahedra) | isometric von Mises |
| `jmax modal3d` | natural frequencies of a solid (3D) | deformed mode shape |
| `jmax thermo` | thermo-mechanical coupling (heat then thermal stress) | von Mises field |
| `jmax topopt` | topology optimization, SIMP + optimality criteria (2D) | grayscale density |
| `jmax topopt3d` | topology optimization (3D tetrahedra) | solid structure |

Examples:

```bash
# Steady heat across a plate, hot left edge, cold right edge.
jmax heat --nx 60 --ny 60 --left 100 --right 0 --out heat.svg

# Cantilever stress under a tip load; reports tip deflection and peak von Mises.
jmax stress --nx 80 --ny 26 --load 1000 --out stress.svg

# Lowest five natural frequencies of a clamped plate; plots mode 1.
jmax modal --nx 60 --ny 20 --modes 5 --show 1 --out mode.svg

# Topology optimization: stiffest cantilever at 40% material.
jmax topopt --nx 80 --ny 40 --volfrac 0.4 --iters 60 --out topology.svg
```

## Fluids

| Command | Solves | Render |
|---|---|---|
| `jmax stokes` | incompressible Stokes (creeping) or Navier-Stokes flow (2D) | speed field + arrows |
| `jmax stokes3d` | 3D incompressible Stokes flow in a box | isometric speed field |
| `jmax unsteady` | time-dependent Navier-Stokes (lid-driven cavity) | flow + energy history |
| `jmax advect` | scalar advection-diffusion, SUPG vs Galerkin | scalar field |
| `jmax lbm` | Lattice-Boltzmann (D2Q9 BGK): lid-driven cavity or Poiseuille channel | speed-field heatmap |

`jmax lbm` is a kinetic-theory CFD solver (the lattice-Boltzmann method, the
approach behind real-time GPU flow tools) that complements the FEM Navier-Stokes
above. It runs a lid-driven cavity or a force-driven channel to steady state and
writes the speed field as a heatmap, static (`--plot`) or interactive (`--html`):

```bash
jmax lbm --case cavity --nx 64 --ny 64 --re 100 --plot cavity.svg
jmax lbm --case channel --html flow   # force-driven Poiseuille, interactive
```

The fluid solvers use a mixed velocity-pressure formulation with
Brezzi-Pitkaranta pressure stabilization, solved by GMRES with a block
(Silvester-Wathen) preconditioner. `jmax stokes --reynolds 0` solves creeping
Stokes flow; any positive Reynolds number switches to the full Navier-Stokes
equations resolved by Picard iteration.

```bash
# Creeping lid-driven cavity (Stokes).
jmax stokes --n 40 --out cavity.svg

# Finite-Reynolds cavity (Navier-Stokes); the primary vortex shifts with Re.
jmax stokes --n 48 --reynolds 200 --out cavity-re200.svg

# Advection-diffusion boundary layer; SUPG stays monotone where Galerkin wiggles.
jmax advect --nx 24 --peclet 50 --out advect.svg
```

## Inverse design and differentiable simulation

| Command | Solves | Render |
|---|---|---|
| `jmax inverse` | recover a hidden conductivity inclusion (one experiment) | true vs recovered |
| `jmax inverse-design` | multiple-excitation inverse (recovers amplitude) | true vs recovered |

These use the adjoint method: the gradient of a least-squares objective with
respect to every per-element conductivity comes from one extra solve, so the
material can be optimized to match observed data. A single experiment localizes a
hidden inclusion but cannot recover its amplitude; several excitation patterns
together over-determine the field and pin the amplitude down.

```bash
jmax inverse-design --n 24 --inclusion 3 --excitations 4 --iters 300 --out recovered.svg
```

## Performance and energy

| Command | Reports |
|---|---|
| `jmax bench` | 3D Poisson solver scaling: DOFs, iterations, time, throughput |
| `jmax energy` | energy-receipt scaling table: FLOPs and estimated joules per solve |

```bash
jmax bench --max-n 48
jmax energy --max-n 64
```

The sparse matrix-vector product is parallel across CPU cores and stays
bit-deterministic. A GPU-resident conjugate gradient (wgpu compute) is available
for the heterogeneous-compute path.

## From the language

Several solvers are callable directly from JMax source (`jmax eval` and
`jmax run`), composing with the linear-algebra surface:

```jmax
# Sparse solve on the GPU (falls back to CPU if no adapter); residual ~ 0.
norm([[4.0,1.0],[1.0,3.0]] * gpu_solve([[4.0,1.0],[1.0,3.0]], [1.0,2.0]) - [1.0,2.0])

# Steady heat on a grid returns a matrix of temperatures.
max(poisson_grid(24, 100.0))      # -> ~7.37 (the 0.0737*f result)

# Natural frequencies of a cantilever as a vector.
modal_freqs(40, 12, 4)
```

Available builtins: `cg_solve`, `bicgstab_solve`, `gmres_solve`, `heat_grid`,
`poisson_grid`, `modal_freqs`, `gpu_solve`, `stokes_lid`.

## How it is built

The stack is two pure-Rust crates. `jmax-sparse` provides CSR/COO matrices,
preconditioned CG / BiCGSTAB / GMRES, ILU(0) and Jacobi preconditioners, and a
subspace-iteration eigensolver. `jmax-fem` builds the meshes, assembles the
element matrices, and implements the physics solvers on top. Each solver is
verified against closed-form results (patch tests, method of manufactured
solutions, and analytic profiles such as Poiseuille flow and beam theory).
