Angular ships with **Vitest** as test runner. Every building block can be tested in isolation. Let's test the `BookCard{:ts}` component (`libs/feature-books/src/lib/book-card/book-card.ts`) the "vanilla" way, directly through `TestBed{:ts}`.

Write your test into a new file. The next tasks will improve this test step by step, so keep it plain for now.

---

- **Create the spec file** `libs/feature-books/src/lib/book-card/book-card.template.spec.ts` with a `describe('BookCard template', ...){:ts}` block and a small test `Book{:ts}` object.

---

- **Create the component** in a `beforeEach(){:ts}`:
  - Configure the `TestBed{:ts}`. `BookCard{:ts}` needs the `BookMarkerStore{:ts}`, so list it as provider.
  - Create the fixture with `TestBed.createComponent(BookCard){:ts}`.
  - Set the required input with `fixture.componentRef.setInput('book', book){:ts}`.
  - Render the template with `fixture.detectChanges(){:ts}`.

---

- **Test the title** `it('should display the book title', ...){:ts}`: query the `<h3>{:html}` with `querySelector{:ts}` and assert that its `textContent{:ts}` contains the title.
- **Test the author** `it('should display the book author', ...){:ts}`: same for the `<h4>{:html}`.

Run `npx nx test feature-books{:bash}`. Both tests must turn green.
