Until now you tested one component at a time. An **integration test** lets several real parts work together: real routes, the real router, real components and the real store. Only the outer boundary (HTTP) is replaced.

You test the navigation from the book list to the detail page.

---

- **Create the spec file** `libs/feature-books/src/lib/book-navigation.spec.ts` with a `describe('Book navigation', ...){:ts}` block and a test `Book{:ts}` object.

---

- **Set up the routes** In a `beforeEach(){:ts}` configure the `TestBed{:ts}`:
  - `provideRouter(bookRoutes, withComponentInputBinding()){:ts}`. `bookRoutes{:ts}` are the real routes of the feature (`book.routes.ts`). The detail page needs the input binding to get the ISBN from the url.
  - a fake `BooksClient{:ts}` returning your book from `list(){:ts}`.

---

- **Test the navigation** `it('opens the detail page when clicking "More" on a book card', ...){:ts}`:
  - Start the app at the root route with `RouterTestingHarness.create('/'){:ts}` (import from `@angular/router/testing`) and `await{:ts}` it.
  - Click the link _More_ with `page.getByRole('link', { name: 'More' }).click(){:ts}`.
  - Assert that the url of the `Router{:ts}` is the detail url of your book. The navigation finishes asynchronously, so use `expect.poll{:ts}`.
  - Assert that the heading of the detail page shows the book title.

Run `npx nx test feature-books{:bash}`.
