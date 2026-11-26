- **Page, route and sidebar** `BooksClient.search(){:ts}` is already prepared. It queries `books?q=...`.

```ts
// book.routes.ts
{
  path: 'search',
  loadComponent: () => import('./book-search-page/book-search-page').then(c => c.BookSearchPage)
},
```

```ts
// libs/ui/src/lib/sidebar/sidebar.ts
pageLinks = signal({ /* ... */ booksSearch: '/books/search' });
```

---

- **Events**

```ts
import { type } from '@ngrx/signals';
import { eventGroup } from '@ngrx/signals/events';

export const bookSearchEvents = eventGroup({
  source: 'Book Search',
  events: {
    searchQueryUpdated: type<{ query: string }>(),
    searchSucceeded: type<Book[]>(),
    searchFailed: type<string>()
  }
});
```

---

- **Store with reducers and event handlers**

```ts
import { Events, on, withEventHandlers, withReducer } from '@ngrx/signals/events';

export const BookSearchStore = signalStore(
  withState(initialState),
  withReducer(
    on(bookSearchEvents.searchQueryUpdated, () => ({ /* loading true */  })),
    on(bookSearchEvents.searchSucceeded, ({ payload: books }) => ({ /* books & loading false */ })),
    on(bookSearchEvents.searchFailed, () => ({ /* loading false */ }))
  ),
  withEventHandlers((_store, events = inject(Events), booksClient = inject(BooksClient)) => ({
    search$: events.on(bookSearchEvents.searchQueryUpdated).pipe(
      switchMap(({ payload }) =>
        booksClient.search(payload.query).pipe(
          mapResponse({
            next: books => /* return searchSucceeded event with books  */,
            error: (error: Error) => {
              console.error(error);
              return bookSearchEvents.searchFailed(error.message);
            }
          })
        )
      )
    )
  }))
);
```

---

- **Dispatch from the page**

```ts
@Component({ /* ... */ providers: [BookSearchStore] })
export class BookSearchPage {
  protected readonly store = inject(BookSearchStore);
  private readonly dispatch = injectDispatch(bookSearchEvents);

  protected search(query: string) {
    this.dispatch.searchQueryUpdated({ query });
  }
}
```

```html
<input
  class="search-field"
  type="search"
  (input)="search($event.target.value)"
/>
```
