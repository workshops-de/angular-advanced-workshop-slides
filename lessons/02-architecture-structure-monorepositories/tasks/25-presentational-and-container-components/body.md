Read the code of the book feature and sort its components into _presentational_ and _container_ components. You do not change any code in this task.

- **Know the difference** A container component knows where data comes from: it injects services or stores, loads data and handles results. A presentational component only gets data through `input(){:ts}` and reports user actions through `output(){:ts}`. It does not know who uses it.

---

- **Inspect the components** Open the folders in `apps/bookmonkey-client/src/app/books/`. For each component, check three things: does it call `inject(){:ts}`, does it declare `input(){:ts}` or `output(){:ts}`, and what does its template render?

---

- **Sort them** Write down for every component whether it is a container or a presentational component. Start with `BooksPage`, `BookCard`, `BookDetailPage` and `BookDetail`.

---

- **Find the mixed ones** Some components do not fit cleanly. Find one that has both `inject(){:ts}` and `input(){:ts}` and decide what you would move out to make it presentational.

---

- **Discuss in your group** Where do containers belong in a feature folder, and where do presentational components belong? Think about which of them you could reuse in another feature.
