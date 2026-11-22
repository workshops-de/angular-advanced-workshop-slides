Turn the Angular CLI workspace into an Nx mono-repo and move both apps into the standard `apps/` folder. Nx packages are already installed, so you only run commands.

- **Initialize Nx** Run `nx init{:bash}` in the project root. Mark `build{:bash}`, `test{:bash}` and `lint{:bash}` as cacheable and skip Nx Cloud and AI agent setup.

---

- **Read the result** Open `nx.json`, the new `project.json` files and the `scripts` in `package.json`. Find out what replaced `angular.json`, where the cache is switched on, and which commands now run through `nx{:bash}`.

---

- **Move the apps** The apps still live in `projects/`. Move `bookmonkey-client` and `rating` to `apps/` with the Nx `move{:bash}` generator, one project after the other.

---

- **Fix the leftover paths** The generator does not update every reference. Run `nx build bookmonkey-client{:bash}` and fix what breaks:
  - the `@workshop-support` path alias in `tsconfig.json`
  - the exposed file path in the `rating` federation config
  - the `tsconfig.spec.json` lookup of the Vitest setup in `vite.config.mts`

---

- **Check graph and cache** Run `nx graph{:bash}` and look at both apps. Then run `nx run-many -t build{:bash}` twice. The second run must read both builds from the cache.
