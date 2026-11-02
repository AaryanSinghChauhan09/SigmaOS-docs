# Principles

SigmaOS is shaped by a deliberately broad catalog of engineering principles. This page
documents each one: what it means in a sentence or two, and how it is applied in the
SigmaOS architecture. Principles are grouped by theme for navigation; the full set
governs design and review decisions across every tier.

---

## Operating Philosophy

### Worse Is Better
Simple, correct, consistent systems ship and survive better than feature-complete but
over-complex ones. SigmaOS picks a small, working, layered core over an all-encompassing
monolith, and lets the long tail of features live in replaceable Tier 3 components.

### Gall's Law
A complex system that works evolved from a simpler system that worked; complex systems
built from scratch rarely work. SigmaOS started from a minimal bootable kernel and grew
tiers incrementally, each proven before the next was added.

### Keep It Simple Stupid (KISS)
Simplicity is a design goal, not a side effect. SigmaOS avoids clever-but-fragile
constructs: a round-robin/priority scheduler, a buddy allocator, a narrow syscall surface.

### You Aren't Gonna Need It (YAGNI)
Do not build for speculative needs. Every component scaffold ships with zero external
dependencies — features and deps are added only when a concrete need exists.

### If It Ain't Broke Don't Fix It
Stable, working subsystems are not rewritten for novelty. The validated DAG and existing
28 CI workflows were audited for backward compatibility rather than rewritten.

### Optimise For Deletion
Code should be easy to remove. Because each component is an independent repo, any Tier 3
component can be deleted without touching the kernel or its siblings.

### Talk Is Cheap Show Me The Code
Design discussion ends in shipped code and tests, not just documents. Phase gates
require passing `cargo check` and test runs, not only written plans.

### Zen Of Python
Clarity, simplicity, and explicitness over implicit magic. SigmaOS code favors explicit
allocations, explicit capability grants, and readable trait surfaces.

### Ninety-Ninety Rule
The first 90% of the code takes 90% of the time; the remaining 10% takes the other 90%.
SigmaOS acknowledges this by treating "scaffold passes check" and "fully wired and
integrated" as distinct milestones across phases.

---

## Architecture & Structure

### Architecture
The system has a deliberate, documented structure rather than an accidental one. SigmaOS
defines a four-tier model with a strict dependency direction, recorded in
[Architecture](Architecture.md).

### Layered Design
Tiers depend only on the tier directly beneath them. The dependency graph is a validated
DAG enforced by a nightly cycle detector, so layering is structural, not aspirational.

### Separation Of Concerns
Each module owns exactly one concern. The desktop layer is split into 11 focused repos
rather than one monolith, each with a single responsibility.

### Single Responsibility
Every struct and type has one job. `SettingsPanel` exposes only `id/title/apply`; the
buddy allocator only allocates frames; the scheduler only schedules.

### Modularity
The system is composed of independently compilable, replaceable modules. 162 repos, each
its own crate, each its own CI.

### Modular Kernel Design
What is not in the kernel is a userland module. Drivers, filesystems, and services run as
components, not in-tree kernel code.

### Minimal Kernel Philosophy
The kernel does as little as possible, very well: scheduling, memory, syscalls,
interrupts. Everything else is promoted to a higher tier.

### Separation Of Mechanism & Policy
Lower tiers provide mechanism; higher tiers decide policy. The scheduler enforces
preemption hooks; a policy module decides fairness goals.

### Plug-In Driver Model
Drivers are pluggable modules, not compiled into the kernel. Hardware drivers
(`SigmaOS-drivers`, `SigmaOS-NVIDIA`) are Tier 3 components over stable interfaces.

### Component Isolation
Components run in isolation and communicate through defined boundaries. Each process has
its own address space; components reach each other only through stable APIs.

### Process Isolation
Each process has its own address space and capability set; syscalls are the only kernel
boundary. A misbehaving component cannot corrupt kernel memory or another process.

### Zero-One-Infinity
Permit zero, one, or many of a thing; avoid arbitrary fixed limits. The component
registry, plugin marketplace, and theme registry are open-ended, not capped.

### Avoid Fragmentation
Keep related concerns together to prevent duplicated, divergent efforts. Shared
libraries (`SigmaOS-common`) and a central theme registry prevent parallel re-implementations.

---

## Object-Oriented & SOLID

### SOLID (O/L/I/D)
The four OOD principles taken together: open-closed, Liskov substitution, interface
segregation, dependency inversion (single responsibility is listed separately). They keep
SigmaOS trait surfaces extensible and substitutable.

### Open-Closed
Modules are open for extension, closed for modification. `theme-engine` adds new renderer
types instead of editing existing ones; `settings` registers new panels rather than
hardcoding them.

### Liskov Substitution
Subtypes must be substitutable for their base types without breaking behavior. Trait
objects (`Box<dyn Widget>`, `Box<dyn SettingsPanel>`) are used wherever a concrete impl
must stand in for the trait.

### Interface Segregation
No client is forced to depend on methods it does not use. `SettingsPanel` exposes only
`id/title/apply`; consumers depend on the narrow trait, not a fat interface.

### Inversion Of Control
Dependencies flow into a module rather than being fetched. Policy objects are passed into
the scheduler; panel implementations are registered, not constructed by the framework.

### Composition Over Inheritance
Build behavior by composing small pieces, not deep class trees. `app-framework`'s
`Container` holds `Vec<Box<dyn Widget>>` rather than a widget inheritance hierarchy.

### Inheritance
Where inheritance is used, it models true "is-a" relationships only. SigmaOS favors
composition, so inheritance is rare and shallow.

### Polymorphism
Different implementations satisfy a common interface. Pluggable scheduler policies,
renderer backends, and package-manager backends all polymorphically implement a trait.

### Encapsulation
Internal state is hidden behind a public interface. Allocators, schedulers, and VFS
objects expose methods, not raw fields.

### Information Hiding
Modules expose what clients need and hide how it is implemented. The VFS trait hides
whether a path is on disk, network, or memory.

### Abstraction
Hide detail behind a concept. "Process", "file", "socket", and "capability" are
abstractions that let higher tiers ignore implementation detail.

### Clear Abstraction
Abstractions should be obvious and minimal, not clever. Each SigmaOS trait surface is
small and named for what it does.

### Demeter Law
A method talks to immediate friends, not strangers. Components operate on their own
data and direct dependencies, avoiding long chains of dotted access.

### General Responsibility Assignment Software Patterns (GRASP)
Assign responsibilities to the class that has the information to fulfill them. The
allocator owns frame accounting; the scheduler owns run queues.

### Booch Method
Object-oriented design via classification, aggregation, and behavior. SigmaOS classifies
components into categories and aggregates them into tiers and subsystems.

### Object-Oriented Programming
Organize a system around objects and their interactions. SigmaOS uses Rust traits and
structs to model objects with behavior and state.

### Command-Query Separation
Methods either perform an action or return data, not both. Settings `apply` mutates;
panel accessors query; they are not conflated.

### Uniform Access
Properties are accessed the same way whether computed or stored. Trait accessors hide
whether a value is stored or derived.

### Uniform Information
All relevant information is presented in a consistent form. Component metadata uses one
`COMPONENTS-entry.toml` schema across all 146 repos.

### Design Pattern
Apply proven recurring solutions. SigmaOS uses registry, strategy (scheduler policy),
plugin, and façade patterns where they fit.

---

## Coupling, Cohesion & Reuse

### Maximise Cohesion
Keep related things together within a module. Each repo owns exactly one concern, so
its contents are tightly related.

### Minimise Coupling
Reduce dependencies between modules. Tiers depend only downward; components depend on
stable APIs, not internals.

### Loose Coupling
Components interact through narrow, stable interfaces. The network stack exposes a thin
trait surface; transport implementations are pluggable and independent.

### Don't Repeat Yourself (DRY)
Each piece of knowledge has a single, authoritative representation. Shared logic lives in
`SigmaOS-common`; CI is a reusable template, not copied per repo.

### Code Reusability
Write code once, use it many places. The reusable `component-ci.yml` template serves all
component repos; shared libraries serve many components.

### Rule Of Three
Refactor on the third duplication, not the first. SigmaOS abstracts shared CI and common
libraries only after a pattern repeats.

### Normalisation
Eliminate redundancy and dependency anomalies. Component metadata is normalized into one
schema and one registry rather than duplicated across docs.

### Refactoring
Continuously improve structure without changing behavior. Phase boundaries are natural
refactor points; stubs are replaced with real cross-crate imports during workspace
unification.

### Boy Scout Rule
Leave the code better than you found it. Each phase cleans up what the previous left
behind (main cleanup, gitmodule normalization, dependency stubs).

---

## Memory & Performance

### Memory Safety
Prevent undefined behavior from memory errors. SigmaOS is written in Rust for ownership
and borrowing guarantees; no buffer overflows or use-after-free by construction.

### Manual Memory Control
Memory is allocated and freed explicitly and deliberately where bounds matter. The
kernel uses a buddy allocator with explicit frame accounting, no garbage collector.

### Memory Discipline
Be deliberate about every allocation. Bounded allocations in kernel space; the dev
profile tunes codegen-units to avoid OOM under swap pressure.

### Virtual Memory Abstraction
Each process sees a contiguous virtual address space via paging. `SimpleVMM` provides the
abstraction; physical frames are managed separately.

### Zero-Copy
Avoid copying data when a handle will do. VFS and network paths pass buffer handles
rather than copying bytes whenever the backing store allows.

### Cache Conscious Design
Lay out data for locality of cache lines. Bounded, contiguous structures are preferred
where hot paths matter; scheduler and allocator data are kept compact.

### Locality Of Reference
Access nearby memory and reuse hot data. Process state and run queues are kept compact
to exploit temporal and spatial locality.

### Efficient Scheduling
Schedule to minimize latency and maximize throughput. The kernel scheduler supports
pluggable EEVDF and BORE policies behind a stable interface.

### Deadlock Avoidance
Design so locks cannot form cycles. The validated dependency DAG and disciplined lock
ordering prevent circular waits across subsystems.

### Race-Free Design
State transitions are atomic, with no time-of-check/time-of-use windows. Privilege and
connection state transitions are guarded against concurrent mutation.

### Safe Concurrency
Concurrency that cannot introduce data races. Rust's ownership model and atomic
operations back SigmaOS's concurrent structures.

### Instrumentation
Measure to understand. Build profiles (dev, release, release-fast, bench) and CI matrix
runs give visibility into compile time and correctness.

### Power Of Ten
Use multiple independent checks; redundancy catches what one check misses. The
dependency graph is validated by three algorithms (DFS, Tarjan, Kahn).

---

## Low-Level & Hardware

### Low Level Control
Direct control over hardware where needed. The kernel's `start_kernel` performs CPU
bootstrap (cli, GDT, IDT, paging) directly.

### Low Level Languages
Use languages close to the metal for system code. SigmaOS uses Rust with `no_std` kernel
targets and direct hardware access where required.

### Direct Device Control
Talk to devices directly through well-defined interfaces. The kernel handles interrupts
and MMIO; drivers are components that use those interfaces.

### Bootloader Integration
Boot the kernel reliably and hand off cleanly. `SigmaOS-bootloader` handles early bring-up
before `start_kernel` takes over.

### Interrupt Handling Discipline
Keep interrupt handlers short and correct: mask, acknowledge, dispatch, return. Handlers
defer real work to userland where possible.

### Cross Compilation
Build for targets other than the host. CI matrices target x86_64 and aarch64; the
microkernel builds for `target_os = "none"` bare-metal targets.

### Portability
Run across architectures. The kernel supports x86_64 and aarch64; features are gated so
architecture-specific code stays isolated.

---

## Testing & Quality

### Deterministic Testing
Tests produce the same result every run, with no dependence on real hardware or timing.
`terminal` tests inject input via `PtyMaster::inject`; `file-manager` tests inject
directory entries.

### Arrange-Act-Assert
Structure tests in three clear phases. SigmaOS test files set up state, perform the
action under test, then assert the outcome.

### Fast-Isolated-Repeatable-Self Validating-Timely (FIRST)
Tests are fast, isolated, repeatable, self-validating, and timely. Each component repo's
tests run in its own CI job with no shared mutable state.

### Defensive Programming
Assume inputs may be wrong and validate them. `settings` checks brightness before
writing; package tools verify signatures before installing.

### Offensive Programming
Fail loudly on programmer error rather than silently recovering. Invalid state is
rejected at the boundary so bugs surface immediately.

### Fail-Fast System
Surface errors as early as possible. Components validate input early and abort with a
typed error instead of proceeding in a bad state.

### Blackbox
Test through the public interface, not internals. SigmaOS tests exercise public APIs,
not private fields, keeping tests robust to internal refactor.

### Spec-Driven Development
Implementation follows a written spec, and the spec gates completion. Phase reports and
ADRs define what each subsystem must do before code is accepted.

---

## Security & Privilege

### Least Privilege
Grant the minimum access needed. The capability framework issues narrow, revocable
permissions; components get only what they require.

### Secure Defaults
Safe out of the box. New components start with no capabilities; security is opt-in,
denied-by-default rather than granted-by-default.

### Race-Free Design
Eliminate time-of-check/time-of-use gaps. Privilege transitions are atomic, closing the
window where a check and use could diverge.

---

## APIs & Evolution

### Stable APIs
Interfaces change slowly and compatibly. Each tier exposes a narrow, independently
versioned trait surface so lower tiers evolve without forcing higher-tier rewrites.

### Pristine Sources
Keep source history clean and authoritative. Phase 1 cleaned the main branch of
non-code assets; documentation lives in the wiki, not the source tree.

### Software Updating Program
The system can update itself safely. `SigmaOS-update` and `SigmaOS-upgrade` provide
update and version-upgrade paths with signature verification.

### Software Support Program
The system is maintainable over time. Phased migration, ADRs, and nightly cycle detection
keep the codebase supportable long term.

---

## Licensing & Openness

### Free And Open Source Software (FOSS)
SigmaOS is free and open source, developed in the open. All repos are MIT-licensed and
publicly developed on GitHub.

### Open Standard And Interoperability
Follow open standards for interoperability. Desktop components implement freedesktop.org
menu and notification specifications; XDG desktop portals are supported.

### User Freedom And Control
Users control their system. Sovereign by design — components are replaceable, themes are
user-installable, and configuration is reproducible via `SigmaOS-dots-configuration`.

### Practical Licensing Awareness
Choose licenses deliberately and keep them consistent. Every SigmaOS repo is MIT; the
workspace declares `license = "MIT OR GPL-2.0"` with awareness of the implications.

---

## Process & Collaboration

### Multiple Repository
The project is split across many repos. SigmaOS is 162 repos so each component has its
own lifecycle, CI, and version.

### Single Repository
The monorepo holds the workspace root, kernel source, and component registry
(`COMPONENTS.toml`), while individual components live as repos/submodules — a hybrid that
keeps a single source of truth for the whole while distributing components.

### Version Control Practices
Everything is in git; changes are committed per work stream with clear messages. Branch
protection, PR reviews, and status checks are standard across repos.

### Transparency & Open Exchange
Development happens in the open. Plans, reports, ADRs, and CI are all visible in the
public repos and this wiki.

### Community-Oriented Development
Build for and with a community. Issue templates, contribution guides, and a
`we-can-fix-everything` coordination repo invite participation.

### Collaboration & Shared Responsibility
No single owner for the whole; responsibility is shared and documented. CI, ownership
files, and ADRs distribute knowledge across contributors.

### Governance And Do-ocracy
Those who do the work decide how it is done. See [Governance](Governance.md) for the
do-ocracy model.

### Meritocracy And Inclusivity
Influence is earned through contribution, while keeping participation open to all. The
code of conduct and contribution guides enforce respectful, inclusive participation.

### Respect & Ethical Participation
Treat everyone with respect; act ethically. The code of conduct is binding on all
contributors and reviews.

### Continuous Improvement & Long Term Sustainability
Improve steadily and keep the project viable long term. Phased migration, nightly checks,
and sustainable CI profiles reflect a long-horizon mindset.

### Discoverability
Make things findable. The component registry, theme registry, plugin marketplace, and
this wiki ensure nothing is hidden.

---

## Deutsch Limit & Neighbors

### Deutsch Limit
There is a limit to how much expressiveness a language can usefully offer before it hurts
more than helps. SigmaOS favors a small, expressive-but-bounded Rust subset over
metaprogramming heavy features.

### Least Astonishment
A system should behave the way users and developers expect; surprises are bugs. Stable
narrow APIs, secure-by-default denial, and consistent naming mean SigmaOS behaves as
predictably as possible.

### Blackbox
Treat modules as black boxes interacting through defined interfaces. Higher tiers use
lower tiers through stable APIs without depending on their internals.

---

## Conflict Resolution

### Theirs
When resolving a merge conflict, take the incoming ("their") side of the change. SigmaOS
uses clear, per-file conflict resolution rather than ad hoc hand-merging, so component
repos converge cleanly when upstreams diverge.

### XTheirs
Take the entire incoming tree wholesale when a full replacement is warranted. For a
component repo being re-imported or reset, `xt`-style full-tree resolution keeps the
authoritative remote copy without partial hand-merges.
