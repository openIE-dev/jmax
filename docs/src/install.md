# Install

JMax ships as a **self-contained prebuilt binary**: no toolchain, no compile step. The quickest path on macOS and Linux is Homebrew; otherwise grab the archive for your platform from the [latest GitHub Release](https://github.com/openIE-dev/jmax/releases/latest). Assets are named `jmax-<version>-<target>.tar.gz` (`.zip` on Windows), each with a `.sha256` checksum.

## Homebrew (macOS / Linux)

```bash
brew install openie-dev/jmax/jmax
jmax --version
```

## Download (macOS / Linux)

```bash
# Pick your target (see the list below); this example is Apple silicon.
V=0.1.2
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
| Windows x86-64 | `x86_64-pc-windows-msvc` (`.zip`) |

## Windows

Download the `x86_64-pc-windows-msvc` `.zip` from the [Releases page](https://github.com/openIE-dev/jmax/releases/latest), unzip it, and put `jmax.exe` somewhere on your `PATH`.

## No install: run it in your browser

[play.charlot-lang.dev](https://play.charlot-lang.dev) runs the JMax evaluator compiled to WebAssembly. Nothing leaves the page.

## Verify

```bash
jmax --version
```
