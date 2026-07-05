# Changelog

All notable changes to this release surface are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versions follow [Semantic Versioning](https://semver.org/).

## [Unreleased]

## [0.1.0] — 2026-07-05

First public release of the JMax binary — a single self-contained tool spanning symbolic math, numerics, simulation, statistics, and visualization (83 subcommands), pure Rust, no BLAS/LAPACK/SciPy.

### Added
- **Numerical linear algebra**: dense LU/QR/Cholesky/SVD; symmetric, general (`eigvals`), sparse Lanczos + shift-invert (`eigs`), and generalized (`geneig`) eigenproblems; general eigenvectors; rank-revealing pivoted QR (`rrqr`); sparse direct solvers (`lusolve`); matrix functions `expm`/`sqrtm`/`logm`; `nmf`, `lowrank`.
- **Tensors**: N-dimensional arrays with broadcasting, `einsum`, and Tucker/HOSVD + CP/PARAFAC decompositions.
- **Roots & optimization**: `fsolve`, nonlinear least squares (`lsq`), constrained and global (`--global`, Differential Evolution) minimization, LP/QP.
- **ODEs, BVPs, SDEs**: adaptive RK45 + stiff solvers; boundary value problems by shooting, Hermite–Simpson collocation, adaptive mesh + continuation, and Chebyshev spectral collocation; stochastic ODEs.
- **Quadrature & interpolation**: 1-D/2-D/3-D adaptive Gauss–Kronrod (`quad`/`quad2`/`quad3`); splines/PCHIP/RBF; Chebyshev spectral methods (`cheb`).
- **Statistics & data**: regression, ANOVA/t-test/Wilcoxon/Levene/Friedman, KDE, bootstrap; time-series forecasting (AR/ARIMA/Holt–Winters); Kalman filter + RTS smoother; dataframes.
- **Signal processing**: FFT, spectrogram, biquad filters, discrete wavelet transform.
- **Simulation**: 2-D/3-D FEM (heat, elasticity, modal, transient), incompressible Stokes/Navier–Stokes, topology optimization, adjoint inverse design, Lattice-Boltzmann CFD.
- **Symbolic**: CAS (`eval`), symbolic ODEs (`dsolve`), polynomial systems via Gröbner bases (`solve-system`), identity verification (`verify`).
- **Visualization**: publication-quality SVG charts, PCA/random projection, interactive SPLOM, animation; WebGPU/interactive bundles.
- Multi-platform prebuilt binaries (macOS arm64/x86_64, Linux x86_64/aarch64 musl, Windows x86_64) with SHA-256 checksums.
- Documentation site at openie-dev.github.io/jmax; runnable examples; browser playground at play.charlot-lang.dev.

[Unreleased]: https://github.com/openIE-dev/jmax/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/openIE-dev/jmax/releases/tag/v0.1.0
