- **Create the config file** The `public` folder is copied into the build output, so the file ends up in `config/books.json` next to `index.html`.

```json
// apps/bookmonkey-client/public/config/books.json
{ "apiUrl": "http://localhost:4730" }
```

---

- **Read it in the slice** The schema lives next to the service. `v.parser(){:ts}` turns it into a function that `httpResource(){:ts}` accepts as `parse`. A wrong file puts the resource into the error state.

```ts
// libs/feature-books/src/lib/books-config.ts
import { httpResource } from "@angular/common/http";
import { Service } from "@angular/core";
import * as v from "valibot";

export const BooksConfigSchema = v.object({
  apiUrl: v.pipe(v.string(), v.url()),
});

export type BooksConfigValue = v.InferOutput<typeof BooksConfigSchema>;

@Service()
export class BooksConfig {
  readonly resource = httpResource<BooksConfigValue>(
    () => "config/books.json",
    {
      parse: v.parser(BooksConfigSchema),
    },
  );
}
```

---

- **Use it in the client** `value(){:ts}` is `undefined` until the file is loaded. A request function that returns `undefined{:ts}` keeps its resource idle. It starts by itself when the configuration arrives. Methods that return an `Observable` have no idle state, so they throw if the configuration is missing.

```ts
// libs/feature-books/src/lib/books-client.ts
readonly #config = inject(BooksConfig).resource;
readonly #baseUrl = computed(() => this.#config.value()?.apiUrl);

#requireBaseUrl(): string {
  const baseUrl = this.#baseUrl();
  if (!baseUrl) {
    throw new Error('The books configuration is not loaded.');
  }
  return baseUrl;
}

getByIsbn(isbn: Signal<string>) {
  return httpResource<Book>(() => {
    const baseUrl = this.#baseUrl();
    return baseUrl ? { url: `${baseUrl}/books/${isbn()}` } : undefined;
  });
}

delete(isbn: string): Observable<void> {
  return this.http.delete<void>(`${this.#requireBaseUrl()}/books/${isbn}`);
}
```

Apply the same pattern to `getAll()`, `create()` and `update()`.

---

- **Fix the test** The client only needs a `resource` with a `value` signal.

```ts
// libs/feature-books/src/lib/books-client.spec.ts
import { signal } from "@angular/core";

providers: [
  provideHttpClient(),
  provideHttpClientTesting(),
  {
    provide: BooksConfig,
    useValue: {
      resource: { value: signal({ apiUrl: "http://localhost:4730" }) },
    },
  },
];
```

---

- **Change the address without a build** With an invalid value the config resource is in the error state and `value(){:ts}` throws or stays empty. No request to the backend is sent. In a pipeline you replace the file in the build output, for example `dist/bookmonkey-client/browser/config/books.json`.

---

- **Write down the decision** Use `docs/adr/0001-feature-structure-and-nx-monorepo.md` as a template. Write down the options, your decision and the consequences, for example one extra request and a file outside the type system.

```bash
npx nx run-many -t lint build test
git add -A
git commit -m "feat: read the api url from a runtime configuration"
```

---

- **Think about registration** `feature-books` is lazy-loaded in `app.routes.ts`. A static import from `app.config.ts` would pull it into the main bundle. Nx forbids that with the rule _Static imports of lazy-loaded libraries are forbidden_. Because `BooksClient` reads the resource directly, no registration in the app is needed.
