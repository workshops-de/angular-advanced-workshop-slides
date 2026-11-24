Your tests start at the bottom of the test pyramid: the `BooksClient{:ts}` (`libs/feature-books/src/lib/books-client.ts`). No component, no template, just a service and a **fake HTTP backend**. Angular's `provideHttpClientTesting(){:ts}` swaps the real HTTP handler for one that never hits the network. Every request is captured, and _you_ answer it with `HttpTestingController{:ts}`.

Run the existing tests once to see the starting point. Do not change them, add a new spec file instead:

```sh
npx nx test feature-books
```

---

- **Create the spec file** `libs/feature-books/src/lib/books-client.parse.spec.ts` with a `describe('BooksClient list()', ...){:ts}` block and a `Book{:ts}` test object.

---

- **Configure the `TestBed`{:ts}** in a `beforeEach(){:ts}`:
  - Provide `provideHttpClient(){:ts}` and `provideHttpClientTesting(){:ts}`.
  - `BooksClient{:ts}` reads the API url from `BooksConfig{:ts}`. Provide a fake `BooksConfig{:ts}` so the test does not need the real runtime configuration.
  - Inject `BooksClient{:ts}` and `HttpTestingController{:ts}`.
- **Verify open requests** with `httpMock.verify(){:ts}` in an `afterEach(){:ts}`. It fails the test when a request was never answered.

---

- **Test the happy path** `it('loads books via GET', ...){:ts}`:
  - Call `list(){:ts}` and collect the result.
  - Expect exactly one `GET{:ts}` request to the books endpoint and answer it with `flush(){:ts}`.
  - Assert that the result equals the flushed books.

---

- **Test the failure path** `it('fails when the response violates the book schema', ...){:ts}`:
  - `list(){:ts}` validates the response against a schema. Flush a book with an invalid `id{:ts}` (not a uuid).
  - Assert that the Observable errors.

Run `npx nx test feature-books{:bash}`. Both new tests must turn green.
