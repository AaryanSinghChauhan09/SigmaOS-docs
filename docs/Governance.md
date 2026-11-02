# Governance

How SigmaOS is run. SigmaOS is a free and open source project developed in the open by a
do-ocratic community. This page defines the model, the contribution path, and the
standards of participation.

---

## Do-ocracy

SigmaOS is governed by **do-ocracy**: those who do the work decide how it is done.

Authority is not assigned by title; it is earned and demonstrated by doing. The person
who writes, reviews, and maintains a component is the person who decides how that
component evolves, within the constraints of the architecture and principles. This keeps
decision-making close to the people with the most context.

In practice this means:

- Proposals are preferred over permission. Open an issue or RFC describing the change and
  start the work.
- Maintainership of a repo is granted to those who have demonstrated sustained,
  high-quality contribution to it.
- Disagreements are resolved by evidence and working code, not by seniority. When two
  approaches are both sound, the one with a passing implementation wins.

The architecture and the [Principles](Principles.md) are the guardrails that keep
do-ocracy from fragmenting the system: any contribution must respect the layered tier
model, the validated DAG, and stable APIs.

---

## Meritocracy and Inclusivity

Influence is earned through contribution, and participation is open to everyone.

- **Meritocracy:** technical decisions are weighted by the quality and track record of the
  contribution, not by who the contributor is outside the project.
- **Inclusivity:** the bar to enter is intentionally low. Good-first-issue labels, clear
  contribution guides, and a self-contained per-repo build make it easy to start. No one
  is turned away because they are new.

These two are paired deliberately: meritocracy without inclusivity becomes a closed
club; inclusivity without meritocracy produces unreviewable churn. SigmaOS keeps both.

---

## Contribution model

1. **Find work.** Browse the [Components](Components.md) registry and open issues.
2. **Propose.** For non-trivial changes, open an RFC (see below). For trivial fixes, open
   a PR directly.
3. **Implement in isolation.** Work against the target `SigmaOS-*` repo; it builds and
   tests independently.
4. **Review.** A maintainer reviews against the architecture and principles. CI must pass
   (check, test, fmt, clippy).
5. **Merge.** Maintainers merge to `main` with branch protection enforced.

See [Contributing](Contributing.md) for the detailed PR process and testing requirements.

---

## Code of conduct

All contributors and maintainers agree to:

- **Be respectful.** Disagree about code, not people. No harassment, discrimination, or
  personal attacks.
- **Be collaborative.** Share context, review others' work in good faith, and credit
  contributions.
- **Be ethical.** Do not use the project to cause harm. Security-sensitive work follows
  the [Security](Security.md) model.
- **Be welcoming.** Help newcomers; assume good intent; document your decisions so others
  can follow.

Violations are reported to the maintainers and addressed per the project's enforcement
process. Participation is a privilege, not a right, and can be revoked.

---

## RFC process

For changes that affect architecture, public APIs, or cross-repo behavior, SigmaOS uses
a lightweight RFC (Request for Comments) process.

1. **Draft.** Author writes an RFC describing the problem, proposed solution, alternatives
   considered, and impact on the tier model and stable APIs.
2. **Comment.** The community and maintainers review. Discussion is open and time-boxed.
3. **Decide.** A maintainer with context on the affected area accepts, rejects, or
   requests revisions, citing evidence and the principles.
4. **Implement.** Once accepted, the RFC becomes an ADR (Architecture Decision Record) in
   the relevant repo's `docs/adr/` and is implemented.

RFCs are versioned and retained so future contributors can understand why a decision was
made. Each Phase 3 desktop repo already carries an `ADR-001` establishing this pattern.

---

## Respect & ethical participation

SigmaOS is built for users' freedom and control, and that extends to how the community
operates. Ethical participation is not optional:

- Respect participants' identities and boundaries.
- Do not weaponize reviews or contributions against contributors.
- Keep security disclosures responsible and coordinated (see [Security](Security.md)).
- Prioritize user freedom and control in design decisions; the system belongs to its
  users.

These standards are binding on everyone, including maintainers, and are the foundation
that makes do-ocracy and meritocracy sustainable.
