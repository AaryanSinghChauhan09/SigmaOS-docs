# Contributing

How to contribute to any `SigmaOS-*` repository. SigmaOS is a constellation of
independently compilable repos, so contributing is local to the repo you care about.

---

## Before you start

- Read [Architecture](Architecture.md) to understand the tier model and where your change
  fits. A change must respect the layered dependency direction.
- Read [Principles](Principles.md) for the design guardrails: one concern per repo, stable
  APIs, deterministic tests, composition over inheritance, YAGNI on dependencies.
- Pick a repo from the [Components](Components.md) registry. Good first repos are the
  Tier 3 components and the desktop repos with well-defined public APIs.

---

## Fork and branch

```bash
# Fork the target repo on GitHub, then:
git clone https://github.com/<your-fork>/SigmaOS-<component>.git
cd SigmaOS-<component>
git remote add upstream https://github.com/AaryanSinghChauhan09/SigmaOS-<component>.git
git checkout -b feat/<short-description>
```

Always branch off `main`. Never commit directly to `main`.

---

## Build and test in isolation

Every component repo is self-contained:

```bash
cargo check --lib
cargo test
cargo fmt -- --check
cargo clippy -- -D warnings
```

A passing `cargo check --lib` and `cargo test` are mandatory before a PR. The reusable
CI template (`component-ci.yml`) runs the same gates, so a clean local run means a clean
CI run.

---

## Coding conventions

- **One concern per repo.** Do not fold a second concern into a component; spin up a new
  repo instead.
- **Zero external deps in scaffolds.** Add a dependency only when a concrete need exists
  (YAGNI). Prefer well-known, actively maintained, pinned-version crates.
- **Public-API-only tests.** Tests exercise the public surface, not private fields, so
  they survive internal refactors (blackbox testing).
- **Deterministic tests.** Inject inputs and state; do not depend on real hardware, real
  PTYs, or wall-clock timing.
- **Memory safety and fail-fast.** Validate inputs at the boundary; return typed errors
  early rather than proceeding in a bad state.
- **Composition over inheritance.** Favor `Vec<Box<dyn Trait>>` over deep class trees.

Match the existing style, naming, and error handling of the repo you are editing. Do not
refactor unrelated code.

---

## Commit and PR

Stage only the files relevant to your change (avoid `git add .`):

```bash
git add src/lib.rs tests/<your_test>.rs
git commit -m "feat: <what changed>"
```

Use [Conventional Commits](https://www.conventionalcommits.org/) prefixes: `feat:`,
`fix:`, `docs:`, `refactor:`, `chore:`, `test:`.

Push and open a PR against `main`:

```bash
git push -u origin feat/<short-description>
gh pr create --base main --title "<short title>" --body "$(cat .github/PULL_REQUEST_TEMPLATE.md)"
```

---

## PR template

Each PR should describe:

- **Summary** — what changed and why.
- **What was tested** — which commands you ran and their results.
- **Architecture impact** — does this touch a tier boundary or a stable API? If so, how?
- **Blocked / out of scope** — anything deferred and why.

A PR that changes cross-repo behavior or a stable API should reference an RFC or ADR (see
[Governance](Governance.md)).

---

## Review process

1. A maintainer with context on the affected area is assigned.
2. Review checks the change against the architecture and principles, not just that it
   compiles.
3. CI must be green: `check`, `test`, `fmt`, `clippy`. Security-sensitive changes may
   also run `cargo-audit` and SAST scans.
4. For cross-repo changes, the `cross-repo-integration.yml` workflow validates the boot
   chain and dependency health.
5. Reviewers request changes or approve. Once approved and CI is green, a maintainer
   merges to `main` with branch protection enforced.

Reviews are about the work, not the person. Reviewers are expected to be specific,
constructive, and grounded in the principles. Authors are expected to respond to feedback
with evidence.

---

## Testing requirements

| Change type | Required |
|-------------|----------|
| New public API | Unit test + integration test exercising the API |
| Bug fix | Regression test that fails before the fix and passes after |
| Refactor | Existing tests stay green; no behavior change |
| Cross-repo | `cross-repo-integration.yml` stays green; cycle detector stays clean |
| Security | No capability expansion without justification; secure defaults preserved |

If a repo has no test framework set up yet, follow the existing pattern in the desktop
repos (two test files per repo, Arrange-Act-Assert, deterministic input injection).

---

## Reporting issues and security

- Feature requests and bugs: open an issue in the relevant `SigmaOS-*` repo using its
  issue templates.
- Security vulnerabilities: follow the coordinated disclosure process in
  [Security](Security.md). Do not open a public issue for security-sensitive bugs.
