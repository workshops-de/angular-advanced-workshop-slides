- **Create the feature and move the logic**

```ts
// state/plugins/with-books.ts
export function withBooks() {
  return signalStoreFeature(
    withState(initialState),
    withComputed(/* ... */),
    withMethods(/* ... */),
    withHooks(/* ... */)
  );
}
```

---

- **Simplify the store**

```ts
// state/book-store.ts
import { signalStore } from '@ngrx/signals';
import { withBooks } from './plugins/with-books';

export const BookStore = signalStore({ providedIn: 'root' }, withBooks());
```
