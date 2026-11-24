The unit and integration tests run against fakes. An **end-to-end test** (E2E) drives the whole app in a real browser against the real backend. You use [Playwright](https://playwright.dev) and let Nx set it up.

---

- **Create an empty E2E project** Add `apps/bookmonkey-client-e2e/project.json` by hand: a project of type `application` with `sourceRoot{:json}` `apps/bookmonkey-client-e2e/src`, no targets, and `implicitDependencies{:json}` on `bookmonkey-client`. It is a sibling of the app, so Vitest of the app does not pick up the Playwright specs.

---

- **Add Playwright** Install `@nx/playwright` with `nx add{:bash}` and run the generator `@nx/playwright:configuration` for the new project. Pass the dev server (`npx nx serve bookmonkey-client`, `http://localhost:4200`) as web server. Check the flags with `--help` first.

---

- **Make the Nx dev server usable for E2E** The `e2e` target waits for the server to be ready. Mark the `serve` target of `bookmonkey-client` as `continuous{:json}`.

---

- **Clean up the generated config** In `playwright.config.mts` keep only the **chromium** project. In the generated example test replace the expectation so it passes for this app: find the link _Books_ in the sidebar.

---

- **Run it** Start the real backend in a second terminal with `npx bookmonkey-api{:bash}`. Run `npx nx e2e bookmonkey-client-e2e{:bash}`. The example test must be green.
