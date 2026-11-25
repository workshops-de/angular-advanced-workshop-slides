- **Initialize the host** Run the generator. The package is already installed, nothing else has to be added.

```sh
npx nx g @angular-architects/native-federation:init --project bookmonkey-client --port 4200 --type dynamic-host
```

The generator moves the old `main.ts` into `bootstrap.ts`, rewires `build` and `serve` in `project.json` to the federation builder and creates `federation.config.mjs`, `tsconfig.federation.json` and `public/federation.manifest.json`.

---

- **Register the remote** The remote runs on port 4201.

```json
// apps/bookmonkey-client/public/federation.manifest.json
{
  "rating": "http://localhost:4201/remoteEntry.json"
}
```

---

- **Make the loader injectable** The token lives in the `core` library, so every feature library can inject it.

```ts
// libs/core/src/lib/remote/remote-module-loader.ts
import { InjectionToken } from '@angular/core';

export type RemoteModuleLoader = <T = unknown>(remoteName: string, exposedModule: string) => Promise<T>;

export const REMOTE_MODULE_LOADER = new InjectionToken<RemoteModuleLoader>('REMOTE_MODULE_LOADER');
```

```ts
// libs/core/src/index.ts
export { REMOTE_MODULE_LOADER } from './lib/remote/remote-module-loader';
export type { RemoteModuleLoader } from './lib/remote/remote-module-loader';
```

`main.ts` waits for `initFederation(){:ts}` and passes the loader on:

```ts
// apps/bookmonkey-client/src/main.ts
import { initFederation } from '@angular-architects/native-federation';

async function main() {
  const { loadRemoteModule } = await initFederation('federation.manifest.json', {
    hostRemoteEntry: { url: './remoteEntry.json' }
  });
  const { bootstrap } = await import('./bootstrap');
  await bootstrap(loadRemoteModule);
}

main().catch(err => console.error(err));
```

```ts
// apps/bookmonkey-client/src/bootstrap.ts
import { registerLocaleData } from '@angular/common';
import localeDe from '@angular/common/locales/de';
import { bootstrapApplication } from '@angular/platform-browser';
import { REMOTE_MODULE_LOADER, RemoteModuleLoader } from '@bookmonkey/core';
import { App } from './app/app';
import { appConfig } from './app/app.config';

registerLocaleData(localeDe);

export function bootstrap(loadRemoteModule: RemoteModuleLoader) {
  return bootstrapApplication(App, {
    ...appConfig,
    providers: [...appConfig.providers, { provide: REMOTE_MODULE_LOADER, useValue: loadRemoteModule }]
  }).catch(err => console.error(err));
}
```

---

- **Make the host start** Both fixes belong into the federation config of the host. `skip{:ts}` keeps a package out of sharing, `shareAngularLocales(){:ts}` adds the locale data to the import map.

```js
// apps/bookmonkey-client/federation.config.mjs
import {
  withNativeFederation,
  fromPackageJson,
  shareAngularLocales
} from '@angular-architects/native-federation/config';

export default withNativeFederation({
  name: 'bookmonkey-client',

  shared: fromPackageJson({
    singleton: true,
    strictVersion: true,
    requiredVersion: 'auto',
    build: 'package'
  })
    .patch(['@angular/*'], { includeSecondaries: { keepAll: true } })
    .override(shareAngularLocales(['de'])),

  skip: [
    'rxjs/ajax',
    'rxjs/fetch',
    'rxjs/testing',
    'rxjs/webSocket',
    '@angular/cdk/schematics'
  ]

  // features: { ... } stays as generated
});
```

Changes of `federation.config.mjs` are only read when the server starts. Restart `nx serve{:bash}` after editing it.
