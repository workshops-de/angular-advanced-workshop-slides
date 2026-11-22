Move the first `loadBooks(){:ts}` call from the page into the store. The store loads the books as soon as it is created.

- **Add `withHooks`** Import `withHooks{:ts}` from `@ngrx/signals` and call `store.loadBooks(){:ts}` in the `onInit{:ts}` hook.

---

- **Clean up the page** Remove `OnInit{:ts}`, `ngOnInit(){:ts}` and the manual call from `BooksPage{:ts}`.
