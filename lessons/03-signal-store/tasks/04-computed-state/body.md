Show the book detail page from the store. A computed `currentBook{:ts}` finds the book that belongs to the route ISBN, so the page needs no own request.

- **Extend the state and add the computed** Add `selectedIsbn: string | null{:ts}` (start: `null{:ts}`). Add `withComputed{:ts}` with `currentBook{:ts}`, which looks up the selected book in `books{:ts}`.

---

- **Add `selectBook`** Add a method to `withMethods{:ts}` that writes the ISBN into `selectedIsbn{:ts}` with `patchState{:ts}`.

---

- **Refactor the detail page** In `BookDetailPage{:ts}` remove the resource. Expose `book = store.currentBook{:ts}` and `isLoading = store.isLoading{:ts}`. An `effect{:ts}` in the constructor calls `store.selectBook(this.isbn()){:ts}` whenever the route param changes. Render `book()` in the template.
