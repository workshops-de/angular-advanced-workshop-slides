- **Know the difference** A quick check for every component: remove it from its page in your mind. Can you render it with fake data and no services? Then it is presentational.

---

- **Inspect the components** Search for these calls in each component file.

```ts
// container: gets its data itself
private booksClient = inject(BooksClient);

// presentational: gets data from outside
book = input.required<Book>();
```

---

- **Sort them** Expected result for the first four components:
  - `BooksPage`: container. It injects the client and loads the books.
  - `BookCard`: presentational. It only has `input(){:ts}` and renders a book.
  - `BookDetailPage`: container. It injects a service and reads the route input.
  - `BookDetail`: presentational. It renders the book it receives.

---

- **Find the mixed ones** The create and edit forms are good candidates. A form that injects a service to save the book does two jobs: it renders the form and it talks to the backend. Move the saving to the page that contains it and give the form an `output(){:ts}` for the submitted value.

---

- **Discuss in your group** Typical answer: pages are containers and live next to the route of their feature. Presentational components such as `BookCard` depend on nothing, so they are the first candidates for a shared `ui` part later in this lesson.
