Load the books into the store with an RxJS stream. `BooksClient` already has a `list(){:ts}` method that returns an Observable.

- **Extend the state** Add `isLoading{:ts}` to the state. It starts with `false{:ts}`.

---

- **Add `loadBooks`** Extend the store with `withMethods{:ts}` and a `loadBooks{:ts}` method built with `rxMethod{:ts}`. Set `isLoading{:ts}` to `true{:ts}`, call `BooksClient.list(){:ts}` in a `switchMap{:ts}`, and handle result and error with `tapResponse{:ts}` from `@ngrx/operators`.

---

- **Trigger loading from the page** Implement `OnInit{:ts}` in `BooksPage{:ts}` and call `store.loadBooks(){:ts}`. Remove the resource and show the loading text with `store.isLoading(){:ts}`. After a book was deleted, call `store.loadBooks(){:ts}` instead of `reload(){:ts}`.
