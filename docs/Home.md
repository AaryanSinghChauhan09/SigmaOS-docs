# SigmaOS

> Sovereign, AI-native operating system built in Rust on a minimal, modular, layered
> microkernel architecture. Free and open source software, developed in the open.

Welcome to the SigmaOS Wiki. This is the central reference for the SigmaOS multi-repo
operating system: its architecture, its component registry, the engineering principles
that shape it, and how to build and contribute to it.

---

## What is SigmaOS?

SigmaOS is a sovereign operating system designed around a small, trustworthy kernel and a
broad ecosystem of independently compilable components. The project follows a strict
**layered design**: each tier depends only on the tier beneath it, never sideways or
upward. Mechanism lives in the lower tiers; policy lives in the higher tiers. This keeps
the kernel minimal, the system modular, and each component replaceable without rippling
breakage across the stack.

SigmaOS is written primarily in Rust for memory safety and zero-cost abstractions, with
no runtime garbage collector in kernel space and manual memory control where it matters.
It is developed as **free and open source software** under permissive licensing (MIT), in
the open, by a do-ocratic community.

---

## Architecture at a Glance

The system is organised into four tiers. Tier 0 is the foundation every other tier boots
on; Tier 3 is everything a user touches. Dependencies flow strictly downward.

```
                          ┌──────────────────────────────────────────────┐
                          │               TIER 3 — USERLAND              │
                          │   146+ component repos + 8 desktop apps      │
                          │   (calculator, file-manager, terminal, media, │
                          │    themes, settings, accessibility, docs ...) │
                          └───────────────────────┬──────────────────────┘
                                                  │ depends on
                          ┌───────────────────────▼──────────────────────┐
                          │            TIER 2 — SYSTEM / DESKTOP            │
                          │   system · desktop · packages · compositor ·  │
                          │   window-manager · terminal · file-manager · │
                          │   text-editor · theme-engine · settings ·     │
                          │   app-framework                               │
                          └───────────────────────┬──────────────────────┘
                                                  │ depends on
                          ┌───────────────────────▼──────────────────────┐
                          │        TIER 1 — FILESYSTEM / NETWORK /        │
                          │                   SECURITY                   │
                          │   VFS · networking stack · privilege sep ·  │
                          │   capability framework · LSM hooks           │
                          └───────────────────────┬──────────────────────┘
                                                  │ depends on
                          ┌───────────────────────▼──────────────────────┐
                          │              TIER 0 — KERNEL                  │
                          │   microkernel: scheduler, memory mgmt (buddy  │
                          │   + paging), syscalls, interrupts, capsicum,  │
                          │   cgroups, boot-to-userspace                  │
                          └──────────────────────────────────────────────┘
                                          │
                                  ┌───────▼───────┐
                                  │   HARDWARE     │
                                  │  x86_64 / aarch64
                                  └───────────────┘
```

Boot chain: **hardware → Tier 0 (kernel) → Tier 1 (filesystem/network/security) →
Tier 2 (system init / desktop / packages) → Tier 3 (userland apps)**.

See [Architecture](Architecture.md) for the full design document.

---

## Quick Links by Tier

All SigmaOS repos live under the `AaryanSinghChauhan09` GitHub org. Each entry below is
an independent, separately versioned repository with its own CI.

### Tier 0 — Kernel
- [SigmaOS-kernel](https://github.com/AaryanSinghChauhan09/SigmaOS-kernel) — microkernel core (scheduler, memory, syscalls, interrupts)
- [SigmaOS-bootloader](https://github.com/AaryanSinghChauhan09/SigmaOS-bootloader) — boot integration and early bring-up

### Tier 1 — Filesystem / Network / Security
- [SigmaOS-filesystem](https://github.com/AaryanSinghChauhan09/SigmaOS-filesystem) — virtual filesystem layer
- [SigmaOS-network](https://github.com/AaryanSinghChauhan09/SigmaOS-network) — networking stack
- [SigmaOS-security](https://github.com/AaryanSinghChauhan09/SigmaOS-security) — privilege separation & security framework

### Tier 2 — System / Desktop / Packages
- [SigmaOS-system](https://github.com/AaryanSinghChauhan09/SigmaOS-system) — init system & orchestration
- [SigmaOS-desktop](https://github.com/AaryanSinghChauhan09/SigmaOS-desktop) — Wayland compositor & desktop
- [SigmaOS-packages](https://github.com/AaryanSinghChauhan09/SigmaOS-packages) — package manager core
- [SigmaOS-compositor](https://github.com/AaryanSinghChauhan09/SigmaOS-compositor) — compositor surface lifecycle
- [SigmaOS-window-manager](https://github.com/AaryanSinghChauhan09/SigmaOS-window-manager) — tiling & workspace management
- [SigmaOS-terminal](https://github.com/AaryanSinghChauhan09/SigmaOS-terminal) — terminal emulator & PTY
- [SigmaOS-file-manager](https://github.com/AaryanSinghChauhan09/SigmaOS-file-manager) — file manager
- [SigmaOS-text-editor](https://github.com/AaryanSinghChauhan09/SigmaOS-text-editor) — text editor & syntax highlight
- [SigmaOS-theme-engine](https://github.com/AaryanSinghChauhan09/SigmaOS-theme-engine) — theme engine
- [SigmaOS-settings](https://github.com/AaryanSinghChauhan09/SigmaOS-settings) — settings panels
- [SigmaOS-app-framework](https://github.com/AaryanSinghChauhan09/SigmaOS-app-framework) — app widget framework

### Tier 3 — Userland (146 component repos)
The full Tier 3 registry — calculators, media players, themes, package tools, networking
utilities, documentation guides, and more — is enumerated in the
[Components](Components.md) page.

---

## Current Status

| Item | Status |
|------|--------|
| Version target | **v0.9.0-alpha** (in development) |
| Phase 1 — Consolidate & classify | ✅ Complete |
| Phase 2 — 7 core subsystem scaffolds | ✅ Complete |
| Phase 3 — 8 desktop repos | ✅ Complete |
| Phase 4 — 146 component repos | ✅ Complete |
| Phase 5 — Finalize (docs, wiki, release prep) | 🚧 In progress |
| Components classified | 1,635 (0 unknowns) |
| Dependency cycles | 0 (DAG validated, 3 algorithms) |
| Subsystem tests | 3,773 across 7 core subsystems |

See the [Roadmap](Roadmap.md) for phase detail and the [Changelog](Changelog.md) for what
each phase delivered.

---

## Where to Go Next

- New to the codebase? Read [Architecture](Architecture.md), then [Getting-Started](Getting-Started.md).
- Want the full component inventory? See [Components](Components.md).
- Want to understand the engineering philosophy? See [Principles](Principles.md).
- Want to contribute? Read [Contributing](Contributing.md) and [Governance](Governance.md).
- Reporting or reviewing security? See [Security](Security.md).
