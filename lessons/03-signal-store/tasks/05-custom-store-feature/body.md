Extract the store logic into a reusable feature with `signalStoreFeature{:ts}`. `book-store.ts` becomes a thin wrapper.

- **Create the feature** Add `libs/feature-books/src/lib/state/plugins/with-books.ts` with a `withBooks(){:ts}` function that returns `signalStoreFeature{:ts}`.

---

- **Move the logic** Move state, computed, methods and hooks into `withBooks(){:ts}` together with their imports.

---

- **Simplify the store** Reduce `BookStore{:ts}` to `signalStore({ providedIn: 'root' }, withBooks()){:ts}`. Components keep working without changes.
