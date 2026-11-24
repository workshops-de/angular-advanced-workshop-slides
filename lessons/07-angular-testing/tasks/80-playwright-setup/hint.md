- **Create an empty E2E project**

```json
// apps/bookmonkey-client-e2e/project.json
{
  "name": "bookmonkey-client-e2e",
  "$schema": "../../node_modules/nx/schemas/project-schema.json",
  "projectType": "application",
  "sourceRoot": "apps/bookmonkey-client-e2e/src",
  "implicitDependencies": ["bookmonkey-client"],
  "targets": {}
}
```

---

- **Add Playwright** The generator writes into the folder of the given project, so `--directory=src` places the tests in `src`.

```bash
npx nx add @nx/playwright
npx nx g @nx/playwright:configuration --project=bookmonkey-client-e2e --directory=src --webServerCommand="npx nx serve bookmonkey-client" --webServerAddress=http://localhost:4200
```

The generator may fail with `ECONNRESET` while downloading the browser. Install it afterwards with `npx playwright install chromium{:bash}`.

---

- **Make the Nx dev server usable for E2E**

```json
// apps/bookmonkey-client/project.json
"serve": {
  "continuous": true
}
```

Add `"continuous": true{:json}` to the existing `serve` target and keep the rest of it.

---

- **Clean up the generated config** Delete the _firefox_ and _webkit_ entries from `projects{:ts}` in `playwright.config.mts`. The example test:

```ts
// apps/bookmonkey-client-e2e/src/example.spec.ts
import { test, expect } from '@playwright/test';

test('has a Books link', async ({ page }) => {
  await page.goto('/');

  await expect(page.getByRole('link', { name: 'Books', exact: true })).toBeVisible();
});
```

`exact: true{:ts}` is needed, because more than one link contains the word _Books_ and Playwright fails on ambiguous locators.
