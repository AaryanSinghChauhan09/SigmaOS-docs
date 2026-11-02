# Roadmap

SigmaOS is being built in phases. Each phase has a defined scope, exit gates, and a
completion report. This page tracks the overall trajectory toward a sovereign,
AI-native operating system.

---

## Current target: v0.9.0-alpha

The immediate milestone is the **v0.9.0-alpha** release. Its targets:

- A clean, minimal main branch holding only source and build config.
- A validated multi-repo architecture across four tiers.
- 7 core subsystem scaffolds, 8 desktop repos, and 146 component repos, all compiling
  independently.
- Complete project documentation: architecture, components, principles, governance,
  security, and this wiki.
- A do-ocratic governance model and contribution path ready for community participation.

v0.9.0-alpha is a developer-preview release: scaffolds compile and tests pass, but
cross-crate wiring and end-to-end boot are the next phase's work.

---

## Phase status

| Phase | Scope | Status |
|-------|-------|--------|
| Phase 1 | Consolidate, classify, scaffold core repos | ✅ Complete |
| Phase 2 | 7 core subsystem scaffolds + integration CI | ✅ Complete |
| Phase 3 | 8 desktop layer repos | ✅ Complete |
| Phase 4 | 146 component repos | ✅ Complete |
| Phase 5 | Finalize: docs, wiki, release prep | 🚧 In progress |

See the [Changelog](Changelog.md) for what each delivered phase produced.

---

## Phase 1 — Consolidate & Classify (Complete)

Scope: turn the monolithic codebase into a classified, dependency-graphed foundation ready
for multi-repo extraction.

- Clean main branch of non-code documentation (down to ~32 root items).
- Classify all 1,635 components with explicit `engine` and `category` fields (0 unknowns).
- Extract an explicit dependency graph (1,672 edges) and validate it as a DAG with zero
  cycles (DFS, Tarjan, Kahn).
- Scaffold 7 core repos + 1 docs repo with boilerplate.
- Design multi-repo CI/CD (5 new workflows, 28 audited, 0 breaking changes).

Exit: all 7 validation gates passed; main branch clean and ready for extraction.

---

## Phase 2 — Core Subsystem Scaffolds (Complete)

Scope: scaffold the 7 core subsystems as independently compilable crates and wire them
into cross-repo CI.

- Scaffold `kernel`, `filesystem`, `network`, `security`, `system`, `desktop`, `packages`.
- All 7 pass `cargo check --lib` with zero errors.
- 3,773 tests across the 7 subsystems.
- Normalize `.gitmodules` to consistent `src_modules/SigmaOS-<name>` naming.
- Add `submodule-cargo-check` matrix job to `cross-repo-integration.yml`.
- Document integration points (e.g. `system`'s stub `FsError` mirroring the filesystem
  crate's variants, marked for Phase 3 replacement).

Exit: boot chain compile check passed; CI wired; Phase 3 entry condition met.

---

## Phase 3 — Desktop Layer (Complete)

Scope: split the desktop stack into focused, independently compilable repos.

- 8 repos: compositor, window-manager, terminal, file-manager, text-editor,
  theme-engine, settings, app-framework.
- All 8 pass `cargo check --lib`; each carries 2 integration test files.
- Zero external dependencies in every scaffold (YAGNI).
- Each repo has a README, ADR-001, CI, and `COMPONENTS-entry.toml`.
- Design principles applied and documented: composition over inheritance, open-closed,
  interface segregation, accessibility-first, deterministic testing.

Exit: desktop layer ready to receive real backend wiring; Phase 4 prerequisites met.

---

## Phase 4 — Component Repos (Complete)

Scope: scaffold the broad userland ecosystem as 146 independent component repos.

- 146 component repos across 22 categories (system services, desktop/UI, themes,
  utilities, networking, security, package management, developer tools, documentation,
  media, and more).
- Each repo: `Cargo.toml`, `LICENSE` (MIT), `README.md`, `src/lib.rs`,
  `COMPONENTS-entry.toml`, CI.
- Manifest written to `PHASE4-MANIFEST.md`.
- Reusable CI template shared across repos.

Exit: 146 repos scaffolded; full component registry in place.

---

## Phase 5 — Finalize (In progress)

Scope: turn the scaffolded multi-repo system into a documented, releasable project.

- Architecture, components, principles, and governance documentation (this wiki).
- Getting-started, contributing, security, and changelog pages.
- Wiki push automation (`gh-wiki-push.sh`).
- Release prep for v0.9.0-alpha.

---

## Long-term vision

SigmaOS aims to be a **sovereign, AI-native operating system**: a system the user fully
controls, composed of replaceable open-source components, with an AI-native layer
available as a first-class subsystem.

The trajectory after v0.9.0-alpha:

1. **Cross-crate workspace unification.** Replace Phase 2/3 stubs with real imports from
   `sigmaos-kernel`, `sigmaos-filesystem`, and `sigmaos-security`. Unify the 7 core
   crates into a Cargo workspace.
2. **End-to-end boot.** A real kernel → filesystem → system init → desktop boot chain
   running on x86_64 and aarch64, validated by `boot-smoke` CI.
3. **Hardware integration.** Wire real drivers, the network stack, and the package
   manager to the lower tiers through stable interfaces.
4. **AI-native subsystem.** Bring the `ai` feature and runtime (`mozjs`,
   `nodejs-binary`, `JS-interpreter`) to first-class status for in-OS AI workloads.
5. **Sovereign packaging.** ISO builder, installer, and update paths producing a
   reproducible, user-controlled distribution.
6. **Community growth.** Do-ocratic governance, RFC process, and contribution model
   scaled to external contributors.

The layered, modular, minimal-kernel design is what makes this trajectory possible: each
step replaces or wires one layer without forcing a rewrite of the rest.
