The workspace contains a second application: `apps/rating`. It is a small **remote** that offers a 5-star rating and is already prepared for **Native Federation**. The BookMonkey client is going to be the **host**. In this task you only set up the host. In the next one you load the rating into it.

---

- **Look at the remote** Start it with `npx nx serve rating{:bash}` and open it in the browser:
  - Open `http://localhost:4201` and click the stars.
  - Read `apps/rating/federation.config.mjs`. Which `name` does the remote have and what does it `exposes{:ts}`?
  - Open `http://localhost:4201/remoteEntry.json`. This file describes the remote at runtime.

---

- **Initialize the host** Let the Native Federation generator prepare the `bookmonkey-client` as a `dynamic-host{:bash}`. Host and remote are connected at runtime through a manifest, no rebuild is needed to add a remote.
  - Run the `init` generator of `@angular-architects/native-federation`.
  - Read what changed: `main.ts`, the new `bootstrap.ts`, `federation.config.mjs`, `public/federation.manifest.json` and `project.json`.
  - Why does `main.ts` only call `initFederation(){:ts}` and load the application afterwards?

---

- **Register the remote** Add the rating to `public/federation.manifest.json`. The key has to be the `name` of the remote, the value is the url of its `remoteEntry.json`.

---

- **Make the loader injectable** The top-level `loadRemoteModule(){:ts}` is deprecated. `initFederation(){:ts}` returns a `loadRemoteModule(){:ts}` of its own that you pass on through dependency injection.
  - Create an `InjectionToken{:ts}` named `REMOTE_MODULE_LOADER{:ts}` in `libs/core` and export it.
  - Let `bootstrap.ts` export a `bootstrap(){:ts}` function that takes the loader and provides it under that token.
  - Let `main.ts` hand over the loader returned by `initFederation(){:ts}`.

---

- **Make the host start** Run the host with `npx nx serve bookmonkey-client{:bash}`. Two things go wrong, and both are typical when you share dependencies at runtime:
  - The build fails with errors like _Could not resolve "node:fs"_. `@angular/cdk` ships an entry point for schematics that only runs in Node.js. Keep it out of the browser bundle.
  - The page stays white and the console says _Unable to resolve specifier '@angular/common/locales/de'_. The host registers German locale data, so that file must be part of the import map.

The BookMonkey client has to look and behave exactly as before. Check `http://localhost:4200/federation.manifest.json` in the network tab.
