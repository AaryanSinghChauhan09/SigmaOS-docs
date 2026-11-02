# Architecture

This document describes the system design of SigmaOS: how the tiers are organised, the
principles that govern the design, and the guarantees each layer provides to the one
above it.

---

## Design Philosophy

SigmaOS is a layered, modular, microkernel-based operating system. Four design tenets
shape every decision:

1. **Minimal kernel philosophy.** The kernel (Tier 0) does as little as possible, and does
   that little thing very well. Scheduling, memory, syscalls, and interrupt dispatch live
   in the kernel; everything else is a userland component.
2. **Modular kernel design.** What is not in the kernel is a separately compilable module.
   Drivers, filesystems, and services run as replaceable components, not monolithic
   in-tree code.
3. **Separation of mechanism and policy.** Lower tiers provide mechanism (how something is
   done); higher tiers decide policy (what is done and when). The scheduler does not
   decide fairness goals; a policy module does.
4. **Layered design.** A tier may depend only on the tier directly beneath it. No tier
   reaches sideways or upward. This keeps the dependency graph a clean DAG and makes every
   layer independently testable.

These tenets are reinforced by a wider set of principles — memory safety, stable APIs,
zero-copy where applicable, least privilege, secure defaults, process isolation, DRY,
YAGNI, KISS, composition over inheritance, loose coupling, and high cohesion. The full
catalog is in [Principles](Principles.md).

---

## Tier Model

```
Tier 0  Kernel          — mechanism only, runs in kernel space
Tier 1  FS/Net/Sec      — core subsystems, kernel-facing mechanism
Tier 2  System/Desktop  — orchestration, desktop, packaging, policy
Tier 3  Userland        — applications and end-user components
```

Dependencies point downward and only downward. A bug or change in Tier 3 can never break
Tier 0; a change in Tier 0 can ripple upward, which is why Tier 0's API surface is kept
small and stable.

---

## Tier 0 — Kernel

The kernel is the only code that runs with full hardware privilege. It is intentionally
small and provides mechanism, not policy.

**Responsibilities:**
- **Scheduling.** A round-robin / priority scheduler with pluggable policy objects
  (EEVDF and BORE policies are feature-flagged). The kernel enforces preemption and
  fairness hooks; the fairness *goals* are supplied by a policy module, keeping the
  scheduler mechanism/policy separated.
- **Memory management.** A buddy allocator for physical frames and a paging layer
  (`SimpleVMM`) for virtual memory abstraction. Memory discipline is explicit: no garbage
  collector in kernel space; allocations are deliberate and bounded.
- **System calls.** A narrow, stable syscall surface. Syscalls are the only sanctioned
  boundary between kernel and userland, enforcing process isolation.
- **Interrupt handling.** A disciplined interrupt path: mask, acknowledge, dispatch to a
  registered handler, return. Handlers are short and defer work to userland where possible.
- **Process isolation.** Each process has its own address space and capability set. The
  kernel does not trust userland; userland cannot reach kernel memory except through
  syscalls.
- **Boot-to-userspace.** `start_kernel` performs CPU bootstrap (cli, GDT, IDT, paging),
  initialises the allocator and scheduler, then hands control to the init system in
  Tier 2.

**Kernel principles applied:** minimal kernel philosophy, modular kernel design,
separation of mechanism & policy, memory safety, manual memory control, memory
discipline, interrupt handling discipline, least privilege, process isolation,
zero-copy where applicable, stable APIs, defensive programming, fail-fast.

---

## Tier 1 — Filesystem / Network / Security

Tier 1 holds the three core subsystems that sit on the kernel and expose mechanism to the
rest of the system. Each is an independent repo and crate.

### Filesystem (`SigmaOS-filesystem`)
A virtual filesystem (VFS) layer. It abstracts concrete filesystem implementations behind
a uniform interface so the rest of the system never knows whether a path lives on a disk,
a network mount, or an in-memory fs. The VFS defines the mechanism (open, read, write,
stat, walk); mount policy and permission checks are layered above.

- **Zero-copy where applicable:** large reads/writes pass buffer handles, not copies,
  whenever the backing store allows it.
- **Stable APIs:** the VFS trait surface is versioned independently of any single
  filesystem implementation.

### Network (`SigmaOS-network`)
The networking stack. It provides socket mechanism, packet routing, and a TLS context. It
does not decide *who* may open a socket — that is a security policy decision owned by
Tier 1 security.

- **Race-free design:** connection state transitions are guarded against concurrent
  mutation.
- **Loose coupling:** the stack exposes a narrow trait surface; transport implementations
  are pluggable.

### Security (`SigmaOS-security`)
Privilege separation and the capability framework. This is where least privilege is
enforced: components receive only the capabilities they need, no more. The security layer
provides mechanism (capability tokens, LSM hooks, sandbox infrastructure) and lets
Tier 2 decide policy (which component gets which capability).

- **Secure defaults:** everything is denied unless explicitly granted.
- **Least privilege:** capabilities are scoped narrowly and revocable.
- **Race-free design:** privilege transitions are atomic, preventing TOCTOU windows.

**Tier 1 principles applied:** information hiding, interface segregation, separation of
mechanism & policy, encapsulation, least privilege, secure defaults, race-free design,
zero-copy, loose coupling.

---

## Tier 2 — System / Desktop / Packages

Tier 2 is where policy lives. It orchestrates the mechanisms from Tier 1 into a coherent,
bootable, usable system. It also holds the desktop stack and the package manager.

### System (`SigmaOS-system`)
The init system and service orchestrator. It defines the boot chain: kernel boots, system
brings up filesystem/network/security, then launches the desktop and package daemons. The
`InitSystem` references Tier 1 filesystem types through documented integration points;
those stubs are replaced by real cross-crate imports during workspace unification.

- **Boot chain validation:** the boot order (kernel → filesystem → system init → desktop)
  is enforced by CI (`boot-chain-validation` job).

### Desktop (`SigmaOS-desktop` + 8 desktop repos)
The desktop layer is split into focused repos rather than one monolith:
`SigmaOS-compositor`, `SigmaOS-window-manager`, `SigmaOS-terminal`, `SigmaOS-file-manager`,
`SigmaOS-text-editor`, `SigmaOS-theme-engine`, `SigmaOS-settings`, and
`SigmaOS-app-framework`. Each owns exactly one concern.

- **Composition over inheritance:** e.g. `app-framework` `Container` holds a
  `Vec<Box<dyn Widget>>` rather than a widget class hierarchy.
- **Open-closed:** new renderers in `theme-engine` add types instead of editing existing
  ones; new settings panels are registered, not hardcoded.
- **Interface segregation:** `settings` exposes a `SettingsPanel` trait with only
  `id/title/apply`.
- **Accessibility first:** `AccessibilityNode` is first-class on every widget.
- **Deterministic testing:** `terminal` tests inject input via `PtyMaster::inject` with no
  real PTY; `file-manager` tests inject directory entries.

### Packages (`SigmaOS-packages`)
The package manager core. It owns dependency resolution, signature verification, and the
high-level DBus API used by Tier 3 software centers. It is a component the rest of the
system depends on, but it is itself a replaceable module, not kernel code.

**Tier 2 principles applied:** separation of concerns, single responsibility, composition
over inheritance, open-closed, interface segregation, deterministic testing, fail-fast,
defensive programming, loose coupling, high cohesion.

---

## Tier 3 — Userland

Tier 3 is everything the user actually runs: 146 component repos plus the desktop apps.
These are independently compilable Rust crates with zero external dependencies in their
scaffolds (YAGNI applied to dependencies). They communicate with lower tiers through stable
trait surfaces and syscalls, never by reaching into kernel memory.

Because each Tier 3 component depends only on the stable APIs of the tiers below, any
component can be replaced, upgraded, or removed without touching the kernel. This is the
payoff of the layered, modular design.

See [Components](Components.md) for the full registry.

---

## Cross-Cutting Guarantees

| Guarantee | How it is enforced |
|-----------|-------------------|
| Layered design | Dependency graph is a validated DAG; CI runs a nightly cycle detector. |
| Process isolation | Per-process address spaces and capability sets; syscalls are the only kernel boundary. |
| Least privilege | Capability framework grants narrow, revocable permissions; defaults deny. |
| Secure defaults | New components start with no capabilities; security is opt-in, not opt-out. |
| Race-free design | Privilege and connection state transitions are atomic; no TOCTOU windows. |
| Zero-copy where applicable | Buffer handles pass through VFS and network paths without copying when the backing store permits. |
| Stable APIs | Each tier exposes a narrow, independently versioned trait surface. |
| Memory safety | Rust ownership in kernel and userland; manual memory control where allocation is bounded. |
| Fail-fast | Components validate input early (e.g. `settings` checks brightness before writing). |
| Defensive programming | Each struct/type has one job; invalid state is rejected at the boundary. |

---

## Dependency Integrity

The entire component graph (1,635 components, 1,672 edges) was validated by three
independent algorithms — DFS cycle detection, Tarjan's SCC, and Kahn's topological sort —
with **zero cycles** found. A nightly CI job (`cycle-detector.yml`) re-checks this so the
DAG property is never silently broken by a new dependency.

This is what makes the layering enforceable rather than aspirational: a cycle would mean
two tiers depend on each other, which the build and the cycle detector would both reject.
