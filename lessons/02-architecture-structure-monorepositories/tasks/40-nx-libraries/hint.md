- **Plan the cut** Use the library category as a rule: components that own a route are _feature_, plain components are _ui_, shared services are _core_, pure functions are _util_.

| Library             | Content                                         |
| ------------------- | ----------------------------------------------- |
| `core`              | `Notifier`, `NotificationHost`                  |
| `ui`                | `Sidebar`                                       |
| `util`              | `bookMatches`, `classifyMarkSegments`           |
| `feature-dashboard` | `Welcome`                                       |
| `feature-books`     | pages, forms, `BooksClient`, routes of `books/` |

`NotificationHost` stays with `Notifier`. The service creates the component and the component injects the service. Two libraries would depend on each other.

---

- **Generate the libraries** The script is written in Node, so it runs on every operating system. Its source is `tools/tasks/generate-libs.mjs`.

```bash
npm run task:generate-libs
```

For every library it runs the Nx generator. This is the command you would type by hand:

```bash
npx nx g @nx/angular:library --name=core --directory=libs/core \
  --importPath=@bookmonkey/core --tags=type:core --prefix=app \
  --unitTestRunner=vitest-analog --skipPackageJson --no-interactive
```

After the generator the script also:

- removes the sample component of the library
- copies the test setup of the app and switches the library tests to the browser mode
- keeps one `@nx/vitest` plugin entry in `nx.json` that covers `libs/**`

---

- **Review the generator output** What you should find:
  - `tsconfig.base.json` is new. It holds the `paths`. The root `tsconfig.json` extends it.
  - Every library has an alias in `paths`, for example `@bookmonkey/core`.
  - Every library has a `project.json` with tags. You use them in the next task.
  - There is no `test` target in the library `project.json`. The `@nx/vitest` plugin infers it from `vite.config.mts`.

```json
// tsconfig.base.json
"paths": {
  "@bookmonkey/core": ["./libs/core/src/index.ts"],
  "@bookmonkey/feature-books": ["./libs/feature-books/src/index.ts"]
}
```

Commit the generated libraries before you go on. The next script touches many files. With this commit the diff of the next step only contains the move, and you can always return to this state (this throws away uncommitted changes):

```bash
git reset --hard
```

```bash
git add -A
git commit -m "chore: generate libraries with nx"
```

---

- **Move the code**

```bash
npm run task:move-to-libs
```

The script (`tools/tasks/move-to-libs.mjs`) uses `git mv{:bash}`, so Git keeps the history. It also:

- deletes the unused `workshop-support/books/book.ts` and the old `public_api.ts`
- replaces the `@workshop-support` imports with `@bookmonkey/util`, `@bookmonkey/ui`, `@bookmonkey/core` and `@bookmonkey/feature-dashboard`
- loads the books routes from the library:

```ts
// apps/bookmonkey-client/src/app/app.routes.ts
{
  path: 'books',
  loadChildren: () => import('@bookmonkey/feature-books').then(mod => mod.bookRoutes)
}
```

- removes `@workshop-support` from `tsconfig.base.json`

`libs/README.md` is the old workshop-support README. It still describes the old structure. Update it if you like.

---

- **Review and commit** A commit is a checkpoint. If something goes wrong later, you can come back to it. Review before you commit. The script already staged everything with `git mv{:bash}`.

```bash
git status
git diff --staged --stat -M
git diff --staged -M -- apps
```

What to look for:

- `-M` lets Git detect moves. Most files should show up as `renamed`, not as deleted and added.
- The diff in `apps/` should only contain changed imports and the lazy route. No other code changed.
- Nothing is left in `workshop-support/`.

Commit the move separately from the generated libraries:

```bash
git add -A
git commit -m "refactor: move code into nx libraries"
```

---

- **Check the public API** Everything that is not exported is private to its library.

```ts
// libs/core/src/index.ts
export { Notifier } from './lib/notifications/notifier';
export type { Notification, NotificationKind } from './lib/notifications/notifier';
```

`NotificationHost` is not exported on purpose. `Notifier` creates it, nobody else needs to know it.

---

- **Check the result** All seven projects must build and test. Every library has its own cache entry now.

```bash
npx nx run-many -t build test
npx nx graph
```

Expected graph:

- `bookmonkey-client` depends on `feature-dashboard`, `feature-books`, `ui` and `core`
- `feature-books` depends on `util`
- `core`, `ui`, `util` and `feature-dashboard` depend on nothing

If a build fails with _Cannot find module '@bookmonkey/…'_, the alias is missing in `tsconfig.base.json` or the path points to a wrong `index.ts`.
