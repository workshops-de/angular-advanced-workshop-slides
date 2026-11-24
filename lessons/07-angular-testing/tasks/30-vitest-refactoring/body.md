The test from the last task works, but it has weak spots: `detectChanges(){:ts}` renders _once_, the assertion checks _once_, and a template that updates a moment later makes the test flaky. Vitest and Angular offer better tools.

Refactor `book-card.template.spec.ts`. The behavior stays the same.

---

- **Wait for the component** Replace `fixture.detectChanges(){:ts}` with `await fixture.whenStable(){:ts}`. Make the `beforeEach(){:ts}` callback `async{:ts}`. It renders the template and waits until the component is stable.

---

- **Poll instead of asserting once** Turn both tests into `async{:ts}` functions and wrap the checked value with `expect.poll(() => ...){:ts}`. Vitest retries the assertion until it passes or times out. Use `await{:ts}` in front of it.

Run `npx nx test feature-books{:bash}`. Compare the old and the new version: Which one would survive a delayed render?
