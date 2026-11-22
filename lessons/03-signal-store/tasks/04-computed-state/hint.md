- **Extend the state and add the computed**

```ts
import { computed } from '@angular/core';
import { withComputed } from '@ngrx/signals';

withState(initialState), // selectedIsbn: null
withComputed(store => ({
  currentBook: computed(() => store.books().find(book => book.isbn === store.selectedIsbn()))
})),
```

---

- **Add `selectBook`**

```ts
withMethods((store, booksClient = inject(BooksClient)) => ({
  selectBook: (isbn: string | null) => patchState(store, { selectedIsbn: isbn }),
  // loadBooks ...
}))
```

---

- **Refactor the detail page**

```ts
export class BookDetailPage {
  private readonly store = inject(BookStore);

  readonly isbn = input('');

  protected readonly book = this.store.currentBook;
  protected readonly isLoading = this.store.isLoading;

  constructor() {
    effect(() => this.store.selectBook(this.isbn()));
  }
}
```

```html
@if (isLoading()) {
<p>Loading book...</p>
} @else if (book(); as book) {
<app-book-detail [book]="book" />
}
```
