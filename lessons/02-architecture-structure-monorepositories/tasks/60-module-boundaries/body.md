The libraries exist, but nothing stops a feature from importing another feature. Enforce the architecture with tags and a lint rule, so that a violation fails `nx lint{:bash}`. The ADR already promised these rules.

- **Set up linting** Open `tools/tasks/setup-lint.mjs` and read what it does. Then run `npm run task:setup-lint{:bash}`. Afterwards `nx lint bookmonkey-client{:bash}` must pass.

---

- **Plan the tags** Every project gets two tags: a `type:{:text}` for the layer and a `scope:{:text}` for the business area. The libraries already have their `type:{:text}` tag. Decide which libraries are `scope:shared{:text}` and which belong to `books` or `dashboard`. Write the allowed dependencies down as a table, one row per tag.

---

- **Add the scope tags** Add the missing `scope:{:text}` tags to the `project.json` of the five libraries.

---

- **Write the rules** Configure `@nx/enforce-module-boundaries{:text}` in `eslint.config.mjs` with one `depConstraints{:ts}` entry per tag from your table. Dependencies point downwards only: a feature never imports another feature, and a scope only uses its own code and `shared{:text}`.

---

- **Break a rule on purpose** Import `@bookmonkey/feature-dashboard` in a file of `feature-books` and run `nx lint feature-books{:bash}`. Read the message, then remove the import. Do the same with a `ui` library importing a `feature`.

---

- **Write down the rules** Replace the follow-up note of `docs/adr/0001-feature-structure-and-nx-monorepo.md` with a section _Module boundaries_ that contains your tag table and the allowed dependencies. Then run `nx run-many -t lint build test{:bash}` and **commit** your work.
