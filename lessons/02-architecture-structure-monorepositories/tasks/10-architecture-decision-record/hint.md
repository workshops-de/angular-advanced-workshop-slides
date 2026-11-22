- **Create the ADR file** Use this skeleton for the file.

```md
<!-- docs/adr/0001-feature-structure-and-nx-monorepo.md -->
# ADR 0001: Feature-Based Structure and Nx Mono-Repo

**Status:** Accepted
**Last Update:** 2026/11/22

## Context

## Considered Options

## Decision

## Consequences
```

---

- **Describe the context** Good context points are facts, not wishes:
  - Code that changes together should live together.
  - Dependencies between parts must be visible and enforceable.
  - Apps share code without publishing packages.
  - Builds and tests stay fast when only a small part changed.

---

- **Compare the options** Use a table per question.

```md
| Option             | Description                          | Downsides                                  |
| ------------------ | ------------------------------------ | ------------------------------------------ |
| **Layers**         | Folders by technical type            | One feature is spread over many folders    |
| **Feature slices** | Folders by use case, shared in data/ | Slices must not import each other          |
```

---

- **Make the decision** One sentence per decision is enough.

```md
1. **Feature slices.** Code is structured by feature, not by layer.
2. **Mono-repo with Nx.** Apps live in `apps/`, libraries in `libs/`.
3. **Rules in code.** Boundaries are enforced with Nx tags and lint rules.
```

---

- **List the consequences** Include good and bad effects, for example locality of changes, a visible dependency graph, faster pipelines and the effort of learning Nx.
