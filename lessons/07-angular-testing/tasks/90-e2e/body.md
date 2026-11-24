Now you test the **core path** of the app as a user does: find a book and open its detail page. Everything is real: browser, app, backend. Keep E2E tests few. They are slow and test a lot at once.

Make sure the backend runs (`npx bookmonkey-api{:bash}`).

---

- **Create the spec file** `apps/bookmonkey-client-e2e/src/books.spec.ts`. Keep `example.spec.ts` as it is.

---

- **Write the core path** in one test `search for a book and open its detail page{:ts}`:
  - Open the app and click the sidebar link _Books_. Check the url ends with `/books`.
  - Enter _Hobbit_ in the search field (role `searchbox{:ts}`).
  - Expect a heading of level 3 that matches _Hobbit_ to be visible.
  - Click the first _More_ link.
  - Expect the url to match a detail url (`/books/detail/` followed by digits) and a level 1 heading matching _Hobbit_.

Run `npx nx e2e bookmonkey-client-e2e{:bash}`.

> The backend delivers only a part of the books. _Hobbit_ is part of it. A search for a book outside would find nothing.
