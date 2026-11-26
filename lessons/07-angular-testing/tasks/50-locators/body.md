So far you tested a single component. Now you test a whole page the way a user uses it: type into the search field and look at what stays on the screen. `BooksPage{:ts}` (`libs/feature-books/src/lib/books-page/books-page.ts`) filters the book list by the search term.

`vitest-browser-angular` is already installed. Its `render(){:ts}` function replaces the manual `TestBed.createComponent(){:ts}` and the `whenStable(){:ts}` call.

In this task you get to know the Vitest browser API. It runs in a real browser and is closely modeled on Playwright: you select elements with locators like `page.getByRole(){:ts}` or `page.getByTestId(){:ts}` and assert with `expect.element(){:ts}`, which retries until the assertion passes. The `App` test in `apps/bookmonkey-client/src/app/app.spec.ts` is a small reference for this style.

---

- **Create the spec file** `libs/feature-books/src/lib/books-page/books-page-search.spec.ts` with a `describe('BooksPage search', ...){:ts}` block and two test `Book{:ts}` objects (for example _Moby Dick_ and _How to win friends_).

---

- **Render the page** Write a helper `renderBooksPage(){:ts}` that calls `render(BooksPage, { providers }){:ts}`. Provide:
  - a fake `BooksClient{:ts}` whose `list(){:ts}` returns both books as `of([...]){:ts}`. Only the HTTP boundary is replaced, the store is real.
  - `provideRouter([]){:ts}`, because the book cards contain router links.

---

- **Test the initial state** `it('shows all books initially', ...){:ts}`: Both titles must be visible. Find them by role: `page.getByRole('heading', { level: 3, name: ... }){:ts}`.

---

- **Test the search** `it('keeps only the books matching the search term', ...){:ts}`:
  - Enter _Moby_ into the search field. It is a `searchbox{:ts}`. Use `locator.fill(...){:ts}`.
  - The _Moby Dick_ heading stays visible.
  - The other heading disappears. Assert `.not.toBeInTheDocument(){:ts}`.

Run `npx nx test feature-books{:bash}`.
