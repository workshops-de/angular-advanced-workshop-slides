- **Add `withHooks`** The hook receives the store, so all methods are available.

```ts
import { withHooks } from '@ngrx/signals';

export const BookStore = signalStore(
  { providedIn: 'root' },
  withState(initialState),
  withMethods(/* ... */),
  withHooks({
    onInit(store) {
      store.loadBooks();
    }
  })
);
```

---

- **Clean up the page** Delete the `OnInit{:ts}` interface, the `ngOnInit(){:ts}` method and its import. Injecting the store is enough.
