- **Set up linting** The script registers the Nx ESLint plugin, writes a baseline `eslint.config.mjs` without a boundary rule and tags both apps with `type:app{:text}`.

```bash
npm run task:setup-lint
npx nx lint bookmonkey-client
```

---

- **Plan the tags** One possible cut. Dependencies point downwards, never sideways between features.

| Source            | May depend on                                       |
| ----------------- | --------------------------------------------------- |
| `type:app`        | `type:feature`, `type:ui`, `type:core`, `type:util` |
| `type:feature`    | `type:ui`, `type:core`, `type:util`                 |
| `type:ui`         | `type:util`                                         |
| `type:core`       | `type:util`                                         |
| `type:util`       | `type:util`                                         |
| `scope:books`     | `scope:books`, `scope:shared`                       |
| `scope:dashboard` | `scope:dashboard`, `scope:shared`                   |
| `scope:shared`    | `scope:shared`                                      |

---

- **Add the scope tags** `core`, `ui` and `util` are shared. The two features keep their own scope.

```json
// libs/core/project.json (same for libs/ui and libs/util)
"tags": ["type:core", "scope:shared"]
```

```json
// libs/feature-books/project.json
"tags": ["type:feature", "scope:books"]
```

```json
// libs/feature-dashboard/project.json
"tags": ["type:feature", "scope:dashboard"]
```

---

- **Write the rules** Add a block for `**/*.ts` files to `eslint.config.mjs`, before the baseline blocks at the end.

```js
// eslint.config.mjs
{
  files: ['**/*.ts'],
  rules: {
    '@nx/enforce-module-boundaries': [
      'error',
      {
        enforceBuildableLibDependency: false,
        allow: [],
        depConstraints: [
          { sourceTag: 'type:app', onlyDependOnLibsWithTags: ['type:feature', 'type:ui', 'type:core', 'type:util'] },
          { sourceTag: 'type:feature', onlyDependOnLibsWithTags: ['type:ui', 'type:core', 'type:util'] },
          { sourceTag: 'type:ui', onlyDependOnLibsWithTags: ['type:util'] },
          { sourceTag: 'type:core', onlyDependOnLibsWithTags: ['type:util'] },
          { sourceTag: 'type:util', onlyDependOnLibsWithTags: ['type:util'] },
          { sourceTag: 'scope:shared', onlyDependOnLibsWithTags: ['scope:shared'] },
          { sourceTag: 'scope:books', onlyDependOnLibsWithTags: ['scope:books', 'scope:shared'] },
          { sourceTag: 'scope:dashboard', onlyDependOnLibsWithTags: ['scope:dashboard', 'scope:shared'] }
        ]
      }
    ]
  }
}
```

---

- **Break a rule on purpose** The lint error names the violated tag constraint. Remove the import afterwards.

```ts
// libs/feature-books/src/lib/books.routes.ts
import { Welcome } from '@bookmonkey/feature-dashboard';
```

```bash
npx nx lint feature-books
```

---

- **Write down the rules** Put the tag table from above into the ADR and replace the follow-up bullet by a statement that violations fail `nx lint`.

```md
## Module boundaries

Every project carries two tags. The lint rule `@nx/enforce-module-boundaries` checks them on each import.

- Dependencies point downwards only. A feature never imports another feature.
- The rules live in `eslint.config.mjs` as `depConstraints`. A new library gets a `type:` and a `scope:` tag first.
```

```bash
npx nx run-many -t lint build test
git add -A
git commit -m "feat: enforce module boundaries with nx tags"
```
