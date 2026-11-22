- **Initialize Nx** Answer the prompts of `nx init{:bash}` like this: cacheable operations `build,test,lint`, no Nx Cloud, no plugins. Nx detects the Angular CLI workspace and converts it.

```bash
npx nx init
```

---

- **Read the result** What you should find:
  - `angular.json` is gone. Each app has a `project.json` with its targets.
  - `nx.json` holds `targetDefaults`. `"cache": true` makes a target cacheable.
  - The scripts in `package.json` call `nx build`, `nx test` and so on instead of `ng`.

```json
// nx.json
{
  "targetDefaults": {
    "build": { "cache": true, "dependsOn": ["^build"] },
    "test": { "cache": true },
    "lint": { "cache": true }
  }
}
```

---

- **Move the apps** Move one project at a time. The generator moves the files and updates `project.json`.

```bash
npx nx g @nx/workspace:move --projectName=bookmonkey-client --destination=apps/bookmonkey-client
npx nx g @nx/workspace:move --projectName=rating --destination=apps/rating
```

---

- **Fix the leftover paths** Replace `projects/` with `apps/` in these places.

```json
// tsconfig.json
"paths": {
  "@workshop-support": ["./apps/bookmonkey-client/src/app/workshop-support/public_api.ts"]
}
```

```js
// apps/rating/federation.config.mjs
'./Rating': './apps/rating/src/app/rating/rating.ts'
```

Vitest looks for `tsconfig.spec.json` in the workspace root. Point the Angular plugin to the one next to the config.

```ts
// apps/bookmonkey-client/vite.config.mts
import { join } from 'node:path';

export default defineConfig(({ mode }) => ({
  plugins: [angular({ tsconfig: join(__dirname, 'tsconfig.spec.json') }), viteTsConfigPaths()],
  // ...
}));
```

---

- **Check graph and cache** After the second run the output says `[local cache]` for both tasks and `Cache: 2/2 hit (100%)`.

```bash
npx nx graph
npx nx run-many -t build
npx nx run-many -t build
```
