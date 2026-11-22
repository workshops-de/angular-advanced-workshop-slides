- **Extend the state and add `loadBooks`** `rxMethod{:ts}` turns a stream pipeline into a method. `tapResponse{:ts}` handles the result without ending the stream on errors.

```ts
import { inject } from '@angular/core';
import { tapResponse } from '@ngrx/operators';
import { patchState, signalStore, withMethods, withState } from '@ngrx/signals';
import { rxMethod } from '@ngrx/signals/rxjs-interop';
import { pipe, switchMap, tap } from 'rxjs';

export const BookStore = signalStore(
  { providedIn: 'root' },
  withState(initialState),
  withMethods((store, booksClient = inject(BooksClient)) => ({
    loadBooks: rxMethod<void>(
      pipe(
        tap(() => patchState(store, { isLoading: true })),
        switchMap(() =>
          booksClient.list().pipe(
            tapResponse({
              next: books => patchState(store, { books, isLoading: false }),
              error: () => patchState(store, { isLoading: false })
            })
          )
        )
      )
    )
  }))
);
```

---

- **Trigger loading from the page**

```ts
export class BooksPage implements OnInit {
  store = inject(BookStore);

  ngOnInit() {
    this.store.loadBooks();
  }
}
```

```html
@if (store.isLoading()) {
<p>Loading books...</p>
}
```
