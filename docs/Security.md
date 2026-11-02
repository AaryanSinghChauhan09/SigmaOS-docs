# Security

SigmaOS's security model. Security is designed in from the kernel up, not bolted on:
least privilege, secure defaults, privilege separation, a capability framework, race-free
state transitions, and LSM hooks.

---

## Threat model in brief

SigmaOS assumes that userland is not fully trusted. A compromised or buggy component must
not be able to escape its process, read another process's memory, or gain privileges it
was not granted. The kernel is the trusted computing base; everything else is untrusted
relative to the kernel and to each other.

---

## Privilege separation

Privilege is separated and minimized across the system:

- **Kernel vs. userland.** The kernel runs with full hardware privilege; userland does
  not. The only sanctioned boundary is the syscall interface.
- **Process isolation.** Each process has its own address space and capability set. No
  process can reach another's memory or the kernel's memory except through syscalls.
- **Per-component capability grants.** Components receive only the capabilities they
  need to do their job, no more. A calculator does not get network sockets; a backup tool
  does not get raw disk access unless explicitly granted.

The `SigmaOS-security` crate (Tier 1) is the home of privilege separation: capability
tokens, sandbox infrastructure, and the policy hooks that Tier 2 uses to decide who gets
what.

---

## Capability framework

SigmaOS uses a capability-based model rather than ambient privilege:

- **Capabilities are tokens**, not ambient process attributes. A component must hold a
  capability to perform the operation it names.
- **Least privilege.** Capabilities are scoped narrowly. "Read this file" is not the same
  as "read all files"; the grant matches the need.
- **Revocable.** Capabilities can be withdrawn, so a component that no longer needs
  access loses it.
- **Secure defaults.** A new component starts with no capabilities. Privilege is opt-in
  and explicitly granted, never granted by default and later restricted.

This implements Least Privilege and Secure Defaults directly: the absence of a grant is
denial.

---

## Secure defaults

The system is safe out of the box by being restrictive by default:

- New components are denied all capabilities until granted.
- Security-hardening features are available and opt-in (`landlock-v4`, `pledge`,
  `capsicum`, `seccomp-strict`, `smack`) via Cargo feature flags.
- Sandbox infrastructure is available for untrusted or network-facing components.
- The package manager verifies signatures before installing
  (`SigmaOS-packages-tools`).

Defaults are chosen so that forgetting to lock something down leaves it locked, not
open.

---

## Least privilege

Least privilege is enforced structurally, not just by convention:

- Capabilities are the unit of privilege, and they are narrow and revocable.
- Processes run with only the capabilities they were granted at start or escalation time.
- The kernel does not trust userland; it validates every syscall against the caller's
  capability set.

This is the principle behind every grant decision: give the minimum that makes the
operation possible, and no more.

---

## LSM hooks

SigmaOS provides Linux Security Module (LSM) hook points so policy can be layered above
the mechanism without modifying the kernel:

- The kernel exposes hook points at the security-relevant operations (file access,
  network bind, capability transition, process exec).
- Tier 1 security supplies the mechanism; Tier 2 supplies the policy that the hooks
  invoke.
- Optional hardening modules (Landlock, pledge, Capsicum, seccomp-strict, SMACK) plug in
  behind these hooks, each gated by a Cargo feature so they can be composed.

This is separation of mechanism and policy: the kernel does not decide what is allowed;
it provides the place where that decision is made.

---

## Race-free design

Security checks must not be vulnerable to time-of-check/time-of-use (TOCTOU) races:

- Privilege transitions are atomic. The check and the use of a capability happen without
  an observable window between them.
- Connection and session state transitions in the network stack are guarded against
  concurrent mutation.
- Filesystem permission checks are evaluated against a stable snapshot, not re-fetched
  between check and operation.

Race-free design closes the class of bugs where an attacker changes state between the
moment the system checks permission and the moment it acts.

---

## Putting it together

| Layer | Security responsibility |
|-------|-------------------------|
| Kernel (Tier 0) | Process isolation, address spaces, syscall validation, LSM hook points |
| Security (Tier 1) | Capability framework, privilege separation, sandbox mechanism, race-free transitions |
| System/Desktop/Packages (Tier 2) | Policy: which component gets which capability; signature verification; secure session management |
| Userland (Tier 3) | Receives only granted capabilities; runs sandboxed for untrusted work; fails fast on invalid input |

The result is a system where compromise of one component is contained to that
component's capabilities, the kernel is never directly reachable by userland, and the
defaults are safe.

---

## Reporting vulnerabilities

Report security vulnerabilities privately and responsibly. Do not open a public issue.
Coordinate disclosure with the maintainers so a fix can ship before details are public.
The project treats security reports with priority and follows coordinated disclosure.
