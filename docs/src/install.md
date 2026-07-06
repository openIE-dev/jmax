# Install

JMax ships as a **self-contained prebuilt binary** — no toolchain, no compile step. Grab the archive for your platform from the [latest GitHub Release](https://github.com/openIE-dev/jmax/releases/latest). Assets are named `jmax-<version>-<target>.tar.gz` (`.zip` on Windows), each with a `.sha256` checksum.

## Download (macOS / Linux)

```bash
# Pick your target (see the list below); this example is Apple silicon.
V=0.1.0
T=aarch64-apple-darwin
curl -fsSL "https://github.com/openIE-dev/jmax/releases/download/v$V/jmax-$V-$T.tar.gz" | tar xz
sudo mv "jmax-$V-$T/jmax" /usr/local/bin/
jmax --version
```

**Targets:**

| Platform | `<target>` |
|---|---|
| macOS (Apple silicon) | `aarch64-apple-darwin` |
| macOS (Intel) | `x86_64-apple-darwin` |
| Linux x86-64 | `x86_64-unknown-linux-gnu` |

Windows binaries are on the way; in the meantime run jmax in your browser (below) or under WSL with the Linux build.

## No install: run it in your browser

[play.charlot-lang.dev](https://play.charlot-lang.dev) runs the JMax evaluator compiled to WebAssembly. Nothing leaves the page.

## Verify

```bash
jmax --version
```
