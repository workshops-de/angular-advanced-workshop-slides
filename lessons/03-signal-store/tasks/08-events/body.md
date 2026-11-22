So far the components called store methods. The Events plugin works like Redux: a view dispatches an event, reducers update the state, event handlers run side effects. You build a Book Search page with it.

- **Create the page and route** Add `BookSearchPage{:ts}` in `libs/feature-books/src/lib/book-search-page/` and register it as lazy route `search{:ts}`. Add a link to the sidebar. The page has a search input and a book grid.

---

- **Define the events** Create `book-search.events.ts` with `eventGroup{:ts}` from `@ngrx/signals/events`:
  - `searchQueryUpdated{:ts}` with `{ query: string }{:ts}`
  - `searchSucceeded{:ts}` with `Book[]{:ts}`
  - `searchFailed{:ts}` with `string{:ts}`

---

- **Build `BookSearchStore`** Create `book-search.store.ts` with state `books{:ts}` and `isLoading{:ts}`.
  - `withReducer{:ts}`: the query sets `isLoading: true{:ts}`, success stores the books, failure resets `isLoading{:ts}`
  - `withEventHandlers{:ts}`: listen to `searchQueryUpdated{:ts}`, call `BooksClient.search(){:ts}` and map the result with `mapResponse{:ts}` to `searchSucceeded{:ts}` or `searchFailed{:ts}`. Log errors with `console.error{:ts}`.

---

- **Dispatch from the page** Provide the store in the page, create the dispatcher with `injectDispatch(bookSearchEvents){:ts}` and call `searchQueryUpdated({ query }){:ts}` on every `input` event. Bind `store.books(){:ts}` and `store.isLoading(){:ts}` in the template.
