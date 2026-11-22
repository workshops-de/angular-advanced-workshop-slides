- **Entity config and `withEntities`** With `collection: 'book'{:ts}` the store gets `bookEntities`, `bookEntityMap` and `bookIds`.

```ts
import { type } from '@ngrx/signals';
import { entityConfig, removeEntity, setAllEntities, withEntities } from '@ngrx/signals/entities';

const bookConfig = entityConfig({
  entity: type<Book>(),
  collection: 'book',
  selectId: (book: Book) => book.isbn
});

signalStoreFeature(
  withImmutableState(initialState), // without books
  withEntities(bookConfig)
  // ...
);
```

See [NgRx Signal Store — Entity Management](https://ngrx.io/guide/signals/signal-store/entity-management).

---

- **`loadBooks` and `currentBook`**

```ts
next: books => patchState(store, setAllEntities(books, bookConfig), { isLoading: false }),
```

```ts
currentBook: computed(() => {
  const isbn = store.selectedIsbn();
  return isbn ? store.bookEntityMap()[isbn] : undefined;
}),
```

---

- **`removeBook`**

```ts
removeBook: rxMethod<string>(
  pipe(
    concatMap(isbn =>
      booksClient.delete(isbn).pipe(
        tapResponse({
          next: () => patchState(store, removeEntity(isbn, bookConfig)),
          error: () => undefined
        })
      )
    )
  )
)
```

```ts
// books-page.ts
deleteBook(book: Book) {
  if (!window.confirm(`Delete "${book.title}"?`)) {
    return;
  }
  this.store.removeBook(book.isbn);
}
```

---

- **Update the page** `const books = this.store.bookEntities();`
