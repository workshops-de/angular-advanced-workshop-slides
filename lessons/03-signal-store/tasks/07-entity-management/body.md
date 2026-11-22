Manage the books as entities. Study the [NgRx Signal Store entity management guide](https://ngrx.io/guide/signals/signal-store/entity-management) first, then refactor `withBooks(){:ts}`.

- **Study the docs** Read about `withEntities{:ts}`, `setAllEntities{:ts}`, `removeEntity{:ts}` and `entityConfig{:ts}`.

---

- **Replace the `books` state** In `state/plugins/with-books.ts` remove `books{:ts}` from the state. Create an `entityConfig{:ts}` for `Book{:ts}` with the collection name `'book'{:ts}` and `selectId{:ts}` returning the ISBN. Add `withEntities(bookConfig){:ts}`.

---

- **Update `loadBooks` and `currentBook`** Replace the books in `loadBooks{:ts}` with `setAllEntities{:ts}`. Let `currentBook{:ts}` read from `bookEntityMap(){:ts}`.

---

- **Add `removeBook`** Add an `rxMethod{:ts}` that calls `BooksClient.delete(){:ts}` and then removes the entity with `removeEntity{:ts}`. Use it in `BooksPage.deleteBook(){:ts}`.

---

- **Update the page** Use `store.bookEntities(){:ts}` instead of `store.books(){:ts}`.
