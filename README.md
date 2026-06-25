# JMax

**A math-native programming language for science, data, and visualization.**

[![Release](https://github.com/openIE-dev/jmax/actions/workflows/release.yml/badge.svg)](https://github.com/openIE-dev/jmax/actions/workflows/release.yml)
[![Quality](https://github.com/openIE-dev/jmax/actions/workflows/quality.yml/badge.svg)](https://github.com/openIE-dev/jmax/actions/workflows/quality.yml)
[![Docs](https://github.com/openIE-dev/jmax/actions/workflows/docs.yml/badge.svg)](https://openie-dev.github.io/jmax)
[![crates.io](https://img.shields.io/crates/v/jmax.svg)](https://crates.io/crates/jmax)
[![License](https://img.shields.io/badge/license-BSL--1.1-blue.svg)](./LICENSE)


JMax is what you'd build if you started from "I want MATLAB / Julia / R / Mathematica, but as a single self-contained tool with no install dance and 2,500 functions built in." It's a complete data-science surface — linear algebra, signal processing, plotting, statistics, ML primitives — projected onto [flowG](https://github.com/openIE-dev/flow-g) so the same code runs on CPU, Apple-silicon Metal, or WebGPU.

This is the **public release surface**. Source is private at [`openIE-dev/jmax-core`](https://github.com/openIE-dev/jmax-core).

**Try it in your browser — no install:** [play.charlot-lang.dev](https://play.charlot-lang.dev) runs the JMax evaluator compiled to WebAssembly. Nothing leaves the page.

## Status

> Release binaries + examples + documentation. JMax is **free to use software**, not an open-source project. See [LICENSE](./LICENSE) for Business Source License 1.1 terms — converts to Apache-2.0 four years after each binary's release date.

## What you get

| Binary | Purpose |
|---|---|
| `jmax` | The JMax CLI: REPL, file runner, formatter, MCP server |

Plus a Rust library, [`jmax`](https://crates.io/crates/jmax), for embedding.

## Install

```bash
# via cargo
cargo install jmax

# via cargo-binstall (prebuilt binary)
cargo binstall jmax

# direct download
curl -fsSL https://github.com/openIE-dev/jmax/releases/latest/download/jmax-$(uname -s)-$(uname -m).tar.gz | tar xz
```

Platform support:

| Platform | Status |
|---|---|
| macOS arm64 (Apple Silicon) | shipping |
| macOS x86_64 | shipping |
| Linux x86_64 (musl) | shipping |
| Linux aarch64 (musl) | shipping |
| Windows x86_64 | shipping |
| WASM (browser) | shipping |

## Hello, JMax

```jmax
# Solve a linear system, look at the spectrum, return the determinant.
let A = [[2.0, 1.0], [1.0, 3.0]]
let b = [5.0, 10.0]
let x = A \ b            # -> [1, 3]
let spectrum = eig(A)    # -> [3.618, 1.382]
det(A)                   # -> 5
```

```bash
jmax run examples/hello/hello.jm     # -> 5.0
```

REPL:

```bash
jmax repl
```

See [`examples/`](./examples/) for linear algebra, signal processing, plotting, science notebooks, and the JMax → flowG projection.

## What makes JMax different

- **2,500+ functions across 82 modules**, all in one binary — linear algebra, FFT, optimization, statistics, ML primitives, file IO, plotting — nothing else to install. The [reference](https://openie-dev.github.io/jmax/reference/builtins.html) documents the 113 most-used as top-level built-ins.
- **`.jm` files run anywhere** — same code on CPU, GPU, WGPU via flowG dispatch.
- **Energy receipts** — every workload optionally records picojoule-per-operation via the substrate-energy layer.
- **MCP server built in** — JMax exposes its computation surface as an MCP server (`jmax mcp`) so any LLM agent can route math through it.
- **Pure Rust** — no Python, no C++ shared libraries, no LAPACK or BLAS to install. JMax compiles to a single binary.

## How it fits

JMax is the **scientific-computing surface** in the openIE-dev family:

- [flowG](https://github.com/openIE-dev/flow-g) — the substrate JMax compiles to
- [Lux](https://github.com/openIE-dev/lux-lang) — general-purpose sibling
- [Joule](https://github.com/openIE-dev/joule-lang) — energy-budgeted compiled sibling
- [JouleDB](https://github.com/openIE-dev/jouledb) — the metered database for persistence

## Documentation

The documentation site and the GitHub Pages book mirror each other:

| | Website | GitHub Pages |
|---|---|---|
| Home / language | [charlot-lang.dev](https://charlot-lang.dev) | [openie-dev.github.io/jmax](https://openie-dev.github.io/jmax) |
| Docs & function reference | [docs.charlot-lang.dev](https://docs.charlot-lang.dev) | [/reference/builtins](https://openie-dev.github.io/jmax/reference/builtins.html) |
| `jmax` CLI reference | [api.charlot-lang.dev](https://api.charlot-lang.dev) | [/reference/cli](https://openie-dev.github.io/jmax/reference/cli.html) |
| Browser playground | [play.charlot-lang.dev](https://play.charlot-lang.dev) | — |

- Examples → [`examples/`](./examples/)
- Embedding API → [docs.rs/jmax](https://docs.rs/jmax)
- Source mirror → [`openIE-dev/jmax-core`](https://github.com/openIE-dev/jmax-core)

## Releases

[GitHub Releases](https://github.com/openIE-dev/jmax/releases) — tagged versions with prebuilt binaries for every supported platform, plus SHA-256 checksums.

## Community

- [Discussions](https://github.com/openIE-dev/jmax/discussions) — Q&A, function requests
- [Issues](https://github.com/openIE-dev/jmax/issues) — bug reports
- Security: see [SECURITY.md](./SECURITY.md)

## License

- **Binaries** — Business Source License 1.1; see [LICENSE](./LICENSE). Free for non-commercial use, internal use by orgs under $1M revenue, security/academic research. Converts to Apache-2.0 four years after each release.
- **Documentation** — CC-BY-4.0
- **Examples** — Apache-2.0
