# Install

`jmax` is published to crates.io and to GitHub Releases.

## Fastest: `cargo binstall`

```bash
cargo binstall jmax
```

`cargo-binstall` fetches the prebuilt binary for your platform from [GitHub Releases](https://github.com/openIE-dev/jmax/releases) without compiling anything.

If you don't have `cargo-binstall` yet:

```bash
cargo install cargo-binstall
```

## From source: `cargo install`

```bash
cargo install jmax
```

This compiles the source. Slower; useful if your platform isn't in our prebuilt list.

## Direct download

```bash
curl -fsSL -o jmax.tar.gz \
  https://github.com/openIE-dev/jmax/releases/latest/download/jmax-$(uname -s)-$(uname -m).tar.gz
tar xzf jmax.tar.gz
mv jmax-*/jmax /usr/local/bin/
```

## Verify

```bash
jmax --version
```
