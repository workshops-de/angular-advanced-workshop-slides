- **Write the core path** The locators are the same you used in the browser-mode tests. Playwright's `expect{:ts}` retries like `expect.element{:ts}` does.

```ts
// apps/bookmonkey-client-e2e/src/books.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Books core path', () => {
  test('search for a book and open its detail page', async ({ page }) => {
    await page.goto('/');
    await page.getByRole('link', { name: 'Books', exact: true }).click();
    await expect(page).toHaveURL(/\/books$/);

    await page.getByRole('searchbox').fill('Hobbit');

    const hits = page.getByRole('heading', { level: 3, name: /Hobbit/ });
    await expect(hits.first()).toBeVisible();

    await page.getByRole('link', { name: 'More' }).first().click();

    await expect(page).toHaveURL(/\/books\/detail\/\d+/);
    await expect(page.getByRole('heading', { level: 1, name: /Hobbit/ })).toBeVisible();
  });
});
```

`first(){:ts}` is needed because the search can return several books. The page also has two `h1` elements, so the heading is found by its name.
