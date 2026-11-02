# Getting Started

This guide gets you from a fresh clone to a building SigmaOS tree and into your first
contribution. SigmaOS is a Rust workspace plus a constellation of independent component
repos.

---

## Prerequisites

| Tool | Minimum | Notes |
|------|---------|-------|
| Rust toolchain | stable | pinned via `rust-toolchain.toml` |
| `cargo` | bundled with Rust | build and test runner |
| `git` | 2.30+ | for submodules and the wiki |
| `make` | any | the `Makefile` drives common tasks |
| `gh` (GitHub CLI) | 2.0+ | optional, for PRs and repo actions |

The workspace ships a `rust-toolchain.toml`, so `rustup` will auto-select the right
toolchain when you enter the directory.

---

## Clone the repository

```bash
git clone https://github.com/AaryanSinghChauhan09/SigmaOS.git
cd SigmaOS
```

To also fetch the core subsystem repos as submodules:

```bash
git submodule update --init --recursive
```

The seven core subsystems (`SigmaOS-kernel`, `SigmaOS-filesystem`, `SigmaOS-network`,
`SigmaOS-security`, `SigmaOS-system`, `SigmaOS-desktop`, `SigmaOS-packages`) live under
`src_modules/` as submodules.

---

## Build from source

SigmaOS is a single large crate with opt-in feature flags. A minimal build:

```bash
cargo build
```

A release build (maximum optimization, fat LTO):

```bash
cargo build --release
```

A fast-iteration release for benchmarking without the full LTO cost:

```bash
cargo build --profile release-fast
```

### Feature flags

The kernel and subsystems are gated behind Cargo features. Notable flags:

```bash
cargo build --features microkernel        # kernel core build (VFS, process, network)
cargo build --features core-shards         # core shards build
cargo build --features full-build          # full build with all modules
cargo build --features realtime            # RTOS variant
cargo build --features drivers             # driver layer
cargo build --features desktop             # desktop layer
cargo build --features wayland             # Wayland backend
```

Security-hardening and performance matrices are also feature-gated
(`landlock-v4`, `pledge`, `capsicum`, `seccomp-strict`, `smack`,
`scheduler-eevdf`, `scheduler-bore`, `cache-lfu`, `cache-arc`, `jemalloc`,
`mimalloc`, `lock-free`, `io-mmap`).

---

## Run the tests

```bash
cargo test
```

For a faster test cycle, the `test` profile uses `codegen-units = 1` with line-tables-only
debug info to avoid OOM on the large codebase. Targeted component tests:

```bash
cargo test --package sigmaos --lib
```

The 7 core subsystem scaffolds together carry 3,773 tests. Each Tier 3 component repo
also builds and tests in isolation with its own CI.

---

## Install

SigmaOS is pre-release (`v0.9.0-alpha` in development). There is no packaged install path
yet, but you can build the kernel binary:

```bash
cargo build --features microkernel --bin sigma_kernel
```

Build artifacts land in `target/`. The ISO builder tooling
(`SigmaOS-ISO`, `SigmaOS-installer`) is part of Tier 3 and is scaffolded but not yet
wired for end-to-end installation.

---

## Dev environment setup

The repo includes a Dev Container (`.devcontainer/`) and a `.cargo/config.toml`. To use
the Dev Container, open the repo in a supporting editor and accept the rebuild prompt.

Common make targets:

```bash
make help        # list available targets
make check       # cargo check
make test        # cargo test
make fmt         # format check
make clippy      # lint
```

(See the `Makefile` for the exact target names available in your checkout.)

---

## First contribution

1. **Pick a repo.** Browse the [Components](Components.md) registry and choose a component
   whose description interests you. Tier 3 repos are the easiest entry points.
2. **Fork and branch.** Fork the target `SigmaOS-*` repo, create a feature branch off
   `main`.
3. **Build and test in isolation.** Every component repo is self-contained:
   ```bash
   cd SigmaOS-<component>
   cargo check --lib
   cargo test
   ```
4. **Follow the conventions.** Zero external deps in scaffolds (YAGNI), one concern per
   repo, deterministic tests, public-API-only test access. See
   [Contributing](Contributing.md) and [Principles](Principles.md).
5. **Open a PR.** Use the repo's PR template; CI runs check, test, fmt, and clippy. See
   [Governance](Governance.md) for how decisions are made.

Good first issues are labeled in the component repos. If you are unsure where to start,
the desktop repos (`SigmaOS-terminal`, `SigmaOS-file-manager`, `SigmaOS-text-editor`)
have well-defined, testable public APIs.
