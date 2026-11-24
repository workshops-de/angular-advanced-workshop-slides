Your tests run in a **real browser** (Vitest Browser Mode with Playwright and Chromium), not in a simulated DOM like jsdom. That is why layout, CSS and real browser APIs behave as in production. Let's use that.

---

- **Find the setup** Open `libs/feature-books/vite.config.mts` and `src/test-setup.ts`. Locate where `test.browser{:ts}` is enabled and where the browser provider is configured.

---

- **Drop the DOM queries** Rewrite `book-card.template.spec.ts` so it does not use `querySelector{:ts}` anymore:
  - Write a helper `renderBookCard(){:ts}` that creates the component, waits with `whenStable(){:ts}` and returns `page.elementLocator(fixture.nativeElement){:ts}` (import `page{:ts}` from `vitest/browser`).
  - Find the title and the author by their **role**: `root.getByRole('heading', { level: 3 }){:ts}`.
  - Assert with `await expect.element(...).toHaveTextContent(...){:ts}`. It retries automatically, so `expect.poll{:ts}` is not needed anymore.

---

- **Test what only a browser can** Add `it('should be visible with a real layout', ...){:ts}` and assert `toBeVisible(){:ts}` on the root locator. In jsdom this check cannot say anything about layout or CSS.

Run `npx nx test feature-books{:bash}`.
