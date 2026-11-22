Create the first NgRx Signal Store for the books feature and let the books page read its list from the store. `@ngrx/signals` is already installed.

- **Create the store** Add `libs/feature-books/src/lib/state/book-store.ts`. Define a state type with a `books{:ts}` array that starts empty and create `BookStore{:ts}` with `signalStore{:ts}` and `withState{:ts}`. Provide it in root.

---

- **Use the store in the books page** Inject `BookStore{:ts}` in `BooksPage{:ts}` and build the filtered list from `store.books(){:ts}` instead of the resource.

> **Note:** The list stays empty for now because nothing fills the store. The next tasks load the data.
