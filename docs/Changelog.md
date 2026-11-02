# Changelog

A record of what each SigmaOS migration phase delivered. Phases 1–4 are complete; Phase
5 (finalize) is in progress.

---

## Phase 1 — Consolidate & Classify (Complete)

Turn the monolithic codebase into a classified, dependency-graphed foundation ready for
multi-repo extraction.

- **Main branch cleaned.** Root directory reduced to ~32 items of source and build
  config; non-code documentation moved out.
- **All 1,635 components classified.** Every component has explicit `engine` and
  `category` fields; zero unknowns remain.
- **Explicit dependency graph extracted.** 1,672 edges across 1,635 components, validated
  as a DAG with **zero cycles** by three independent algorithms (DFS, Tarjan's SCC, Kahn's
  topological sort).
- **7 core repos + 1 docs repo scaffolded.** Kernel, filesystem, network, security,
  system, desktop, packages, plus a docs repo — each with README, Cargo.toml, LICENSE,
  CI, and governance boilerplate.
- **Multi-repo CI/CD designed.** 5 new workflows (component-ci, component-security,
  cross-repo-integration, cycle-detector, docs-sync); 28 existing workflows audited with
  zero breaking changes.

All 7 Phase 1 validation gates passed.

---

## Phase 2 — Core Subsystem Scaffolds (Complete)

Scaffold the 7 core subsystems as independently compilable crates and wire them into
cross-repo CI.

- **7 subsystem repos scaffolded:** kernel, filesystem, network, security, system,
  desktop, packages.
- **All 7 pass `cargo check --lib`** with zero errors.
- **3,773 tests** across the 7 subsystems (kernel 3,022; filesystem 549; network 28;
  security 39; system 54; desktop 51; packages 30).
- **`.gitmodules` normalized** to consistent `src_modules/SigmaOS-<name>` naming across
  all 7 entries.
- **Cross-repo integration CI updated** with a `submodule-cargo-check` matrix job; the
  `integration-summary` and `component-repo-trigger` jobs now gate on it.
- **Boot chain integration point documented** in `system/repo/src/lib.rs` (stub `FsError`
  mirroring the filesystem crate's variants, marked for Phase 3 replacement).

Boot chain compile check passed; Phase 3 entry condition met.

---

## Phase 3 — Desktop Layer (Complete)

Split the desktop stack into focused, independently compilable repos.

- **8 desktop repos scaffolded:** compositor, window-manager, terminal, file-manager,
  text-editor, theme-engine, settings, app-framework.
- **All 8 pass `cargo check --lib`.** Each carries 2 integration test files exercising
  the public API.
- **Zero external dependencies** in every scaffold (YAGNI applied to deps).
- **Per-repo contents:** `Cargo.toml`, `LICENSE` (MIT), `README.md`, `src/lib.rs`,
  `tests/`, `docs/adr/ADR-001-*.md`, `COMPONENTS-entry.toml`, CI.
- **Design principles applied and documented:** composition over inheritance
  (`Container` holds `Vec<Box<dyn Widget>>`), open-closed (new renderers/panels add
  types, not edits), interface segregation (`SettingsPanel` exposes only
  `id/title/apply`), accessibility-first (`AccessibilityNode` first-class on every
  widget), deterministic testing (`PtyMaster::inject`, injected directory entries).

Desktop layer ready to receive real backend wiring; Phase 4 prerequisites met.

---

## Phase 4 — Component Repos (Complete)

Scaffold the broad userland ecosystem as 146 independent component repos.

- **146 component repos scaffolded** across 22 categories: system services (19),
  desktop/UI (31), themes (10), utilities (28), networking (5), security (3), package
  management (7), developer tools (5), documentation (9), media (4), graphics (2), file
  management (3), libraries (3), runtime (3), panel applets (3), build tools (1),
  hardware (1), accessibility (1), configuration (1), compatibility (1), productivity
  (1), and tools (5).
- **Per-repo contents:** `Cargo.toml`, `LICENSE` (MIT), `README.md`, `src/lib.rs`,
  `COMPONENTS-entry.toml`, CI (`ci.yml` + shared `component-ci.yml`).
- **Manifest written** to `PHASE4-MANIFEST.md` (146 repos enumerated).
- **Reusable CI template** shared across all component repos.
- **Total project repos now 162:** 2 kernel, 3 core subsystems, 11 system/desktop,
  146 userland.

Full component registry is in [Components](Components.md).

---

## Phase 5 — Finalize (In progress)

Turn the scaffolded multi-repo system into a documented, releasable project.

- **Wiki authored:** Home, Architecture, Components, Principles, Getting-Started,
  Roadmap, Governance, Contributing, Security, and this Changelog.
- **Wiki push automation** (`gh-wiki-push.sh`) provided.
- **Release prep** for v0.9.0-alpha.

Phase 5 is the current active phase.

---

## Repository totals

| Phase | Repos added | Running total |
|-------|-------------|---------------|
| Phase 1 | 7 core + 1 docs | 8 |
| Phase 2 | 7 subsystems (incl. kernel) | 16 |
| Phase 3 | 8 desktop | 24 |
| Phase 4 | 146 userland | 170 |
| Phase 5 | (docs only) | 170 |

(Counts overlap where Phase 1 scaffolds were formalized in Phase 2; the canonical tally
of 162 distinct SigmaOS repos is recorded in [Components](Components.md).)
