- **Create the library**

```bash
npx nx g @nx/angular:library architecture-tests --directory=libs/architecture-tests --tags=type:test --unitTestRunner=vitest-analog --dry-run
```

Check the `--dry-run` output, then run it without the flag. If the generator adds a sample component, a path alias in `tsconfig.base.json` or changes to `package.json`, undo these changes. The library imports no code, it only reads files.

---

- **Make it run in Node** The relevant part of `libs/architecture-tests/vite.config.mts`:

```ts
test: {
  name: 'architecture-tests',
  watch: false,
  globals: true,
  environment: 'node',
  include: ['{src,tests}/**/*.{test,spec}.{js,mjs,cjs,ts,mts,cts,jsx,tsx}'],
  setupFiles: ['src/test-setup.ts']
}
```

There must be no `browser{:ts}` block and no `browserMode: true{:ts}` in `test-setup.ts`.

---

- **Write two tests** `nx test` starts in the project folder, so the `tsconfig` path starts with `../../`. The folder patterns are relative to the workspace root.

```ts
// libs/architecture-tests/src/architecture.spec.ts
import { projectFiles } from 'archunit';

const tsConfig = '../../tsconfig.base.json';

describe('architecture', () => {
  it('has no circular dependencies', async () => {
    const rule = projectFiles(tsConfig).inFolder('libs/**').should().haveNoCycles();

    await expect(rule).toPassAsync();
  });

  it('feature-books does not depend on feature-dashboard', async () => {
    const rule = projectFiles(tsConfig)
      .inFolder('libs/feature-books/**')
      .shouldNot()
      .dependOnFiles()
      .inFolder('libs/feature-dashboard/**');

    await expect(rule).toPassAsync();
  });
});
```

---

- **Fix what the test finds** `notifier.ts` imports `NotificationHost{:ts}`, and `notification-host.ts` imports `Notifier{:ts}` back. Break the cycle:
  - Move the types `Notification{:ts}` and `NotificationKind{:ts}` into their own file `notification.ts` and import them from there. Update `libs/core/src/index.ts` to export the types from the new file.
  - Let `NotificationHost{:ts}` stop injecting `Notifier{:ts}`. Give it an `input.required<Notification[]>(){:ts}` named `notifications` and an `output<number>(){:ts}` named `dismissed`. Use both in `notification-host.html`.
  - In `Notifier{:ts}` set the input with `hostRef.setInput('notifications', ...){:ts}`, subscribe to `dismissed` and call `detectChanges(){:ts}` after each change (a small private method `renderHost(){:ts}` avoids repeating it).

---

- **Cache the test**

```json
// libs/architecture-tests/project.json
"targets": {
  "test": {
    "inputs": [
      "default",
      "{workspaceRoot}/tsconfig.base.json",
      "{workspaceRoot}/libs/**/*.ts",
      "{workspaceRoot}/apps/**/*.ts",
      "!{workspaceRoot}/libs/**/*.spec.ts",
      "!{workspaceRoot}/apps/**/*.spec.ts"
    ]
  }
}
```
