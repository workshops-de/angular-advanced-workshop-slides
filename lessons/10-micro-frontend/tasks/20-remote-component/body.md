The host is ready. Now you load the `Rating` component of the remote at runtime and show it on the book detail page. The remote exposes it as `./Rating`. Host and remote share Angular as a singleton, so the host can render the component like one of its own.

---

- **Create a wrapper component** `RemoteRating{:ts}` in `libs/feature-books/src/lib/remote-rating/remote-rating.ts` with the selector `app-remote-rating`.
  - Inject the `REMOTE_MODULE_LOADER{:ts}` you created in the last task.
  - Give it a `value = model(0){:ts}`, so a parent can bind to it with `[(value)]{:html}`.

---

- **Load the module** Use a `resource(){:ts}` whose `loader{:ts}` calls the loader with the remote name `rating` and the exposed module `./Rating`. The result contains the exported class `Rating{:ts}`. Describe its shape with a small type, do not import anything from `apps/rating`.

---

- **Render the loaded component** The class is only known at runtime, so the template cannot use its selector.
  - Put an `<ng-container #host>{:html}` into the template and read it with `viewChild.required(){:ts}` as `ViewContainerRef{:ts}`.
  - In an `effect(){:ts}`, create the component with `createComponent(){:ts}` as soon as the resource has a value.
  - Connect `value` of your wrapper with the `value` model of the remote using `twoWayBinding(){:ts}` in the `bindings{:ts}` option.

---

- **Handle a missing remote** What happens if the remote is not running? Show _The rating is currently not available._ and a loading hint while waiting. Mind that `value(){:ts}` of a resource throws in the error state, so guard it with `hasValue(){:ts}`.

---

- **Use it on the detail page** Add `<app-remote-rating>{:html}` to `BookDetailPage{:ts}` below the book and bind it to a local `rating = signal(0){:ts}` with `[(value)]{:html}`. The rating is not saved anywhere yet.

---

- **Repair the existing test** `book-navigation.spec.ts` renders the detail page and now fails because nobody provides `REMOTE_MODULE_LOADER{:ts}`. Provide a loader that never resolves, this test is about navigation. Add a spec for `RemoteRating{:ts}` that checks the hint when the loader rejects.

---

Start remote and host in two terminals (`npx nx serve rating{:bash}` and `npx nx serve bookmonkey-client{:bash}`), open a book and click the stars. Then stop the remote and reload the page: the book is still shown, only the rating is replaced by the hint.
