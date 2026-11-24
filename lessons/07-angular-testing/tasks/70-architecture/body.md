Code structure erodes silently. **Architecture tests** turn the rules of your architecture into tests that fail when someone breaks them. You use [ArchUnitTS](https://github.com/LukasNiessen/ArchUnitTS) (package `archunit`, already installed).

ArchUnitTS reads your source files through the file system. That needs Node, but all other projects here run in the browser. So the tests live in their own library.

---

- **Create the library** Generate `architecture-tests` with `@nx/angular:library` in `libs/architecture-tests`, tag `type:test`, unit test runner `vitest-analog`. Check the flags with `--help` first.

---

- **Make it run in Node** The generator sets up a test config like in the other libraries. Open the generated `vite.config.mts` and make sure that **no** browser is configured (`test.browser{:ts}`) and that the `environment{:ts}` is `node{:ts}`. Remove everything the generator created that you do not need (for example a sample component).

---

- **Write two tests** in `libs/architecture-tests/src/architecture.spec.ts` with `projectFiles(...){:ts}` from `archunit`:
  - There are **no circular dependencies** in `libs/**`.
  - Files in `feature-books` **do not depend on** files in `feature-dashboard`.

Run `npx nx test architecture-tests{:bash}`.

---

- **Fix what the test finds** The first test is red. It found a real cycle in `libs/core`. Read the failure message, break the cycle and run the test again until it is green.

---

- **Prove that the second test works** Temporarily import something from `feature-dashboard` in a file of `feature-books`. The test must turn red. Remove the import afterwards.

---

- **Cache the test** `nx test architecture-tests{:bash}` is cached, but Nx does not know that the test reads the sources of **all** libraries. Run it twice and watch the cache hit. Then change a file in `feature-books` and run it again: the cache must not be used. Extend `targets.test.inputs{:json}` in the `project.json` of the library accordingly.

> The second rule overlaps with the Nx module boundaries from the architecture lesson. Architecture tests are more flexible: you can test your **own** layers without being bound to tags and vertical slices.
