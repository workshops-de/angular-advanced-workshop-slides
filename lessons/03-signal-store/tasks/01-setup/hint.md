- **Create the store** `signalStore{:ts}` takes an optional config first. `providedIn: 'root'{:ts}` makes it available everywhere.

```ts
// libs/feature-books/src/lib/state/book-store.ts
import { signalStore, withState } from '@ngrx/signals';
import { Book } from '../book';

type BookStoreState = {
  books: Book[];
};

const initialState: BookStoreState = {
  books: []
};

export const BookStore = signalStore({ providedIn: 'root' }, withState(initialState));
```

---

- **Use the store in the books page** Every state property becomes a signal on the store.

```ts
// books-page.ts
store = inject(BookStore);

booksComputed = computed(() => {
  const markTerm = this.bookMarkerStore.markTerm();
  const books = this.store.books();

  return books.filter(book => bookMatches(book, markTerm));
});
```
