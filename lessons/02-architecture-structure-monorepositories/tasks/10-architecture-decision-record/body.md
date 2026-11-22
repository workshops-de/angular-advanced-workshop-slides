Write down the architecture decisions of this lesson in an ADR, before you change any code. The decisions are: a feature-based structure and a mono-repo with Nx.

- **Create the ADR file** Add `docs/adr/0001-feature-structure-and-nx-monorepo.md`. The number keeps decisions in order, the name says what was decided.

---

- **Describe the context** Add a title and the header with `Status` and `Last Update`. Then explain the problem: the app grows, a second app exists and several teams work in the same code. List what you need from the solution.

---

- **Compare the options** Compare at least two options for each question in a table with a short description and the downsides: layers or feature slices for the code structure, and multi-repo, mono-repo without a tool or mono-repo with Nx for the repository.

---

- **Make the decision** Write down what you decide. Keep it short and use a numbered list.

---

- **List the consequences** Name what gets better and which trade-offs you accept. Add a note that the rules for module boundaries follow later.
