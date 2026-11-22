Split the code of `bookmonkey-client` into libraries. Two npm scripts do the repetitive work. You decide the cut, run the scripts and check the result.

- **Plan the cut** Sort the code into five libraries. Use the folder `workshop-support/` and the folder `books/` as input:
  - `core` for the `Notifier` and its `NotificationHost`
  - `ui` for the `Sidebar`
  - `util` for the pure functions `bookMatches{:ts}` and `classifyMarkSegments{:ts}`
  - `feature-dashboard` for the `Welcome` page
  - `feature-books` for everything inside `books/`

---

- **Generate the libraries** Open `tools/tasks/generate-libs.mjs` and find the `nx g @nx/angular:library{:bash}` call. Check that your plan matches the list of libraries. Then run `npm run task:generate-libs{:bash}`.

---

- **Review the generator output** Look at the changes in Git. Find out what happened to `tsconfig.json`, `tsconfig.base.json` and `nx.json`, and what a generated library consists of. Then **commit the generated libraries**. The next script moves a lot of files. A commit is your safety net: if anything goes wrong, you are one command away from a working state.

---

- **Move the code** Open `tools/tasks/move-to-libs.mjs`, read what it moves where, and run `npm run task:move-to-libs{:bash}`. Afterwards the folder `workshop-support/` is gone and the old `@workshop-support` alias is removed.

---

- **Review and commit** The script changed a lot. Take your time and review every change before you commit. Check that files were moved and not rewritten, and that only imports changed in the apps. Then **commit the move** as its own commit. Small commits with a clear message are how you work in a real team, and they let you undo one step without losing the other.

---

- **Check the public API** Open the `src/index.ts` of each library. Find out why `NotificationHost` is not exported.

---

- **Check the result** Run `nx run-many -t build test{:bash}`. Open `nx graph{:bash}` and check that `feature-books` only depends on `util`.
