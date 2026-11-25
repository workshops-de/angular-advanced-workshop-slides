- **Create a wrapper component** and **Load the module** `resource(){:ts}` calls the loader once. The type `RatingModule{:ts}` only describes what the host needs from the remote.

```ts
// libs/feature-books/src/lib/remote-rating/remote-rating.ts
import {
  Component,
  effect,
  inject,
  model,
  resource,
  Type,
  twoWayBinding,
  viewChild,
  ViewContainerRef
} from '@angular/core';
import { REMOTE_MODULE_LOADER } from '@bookmonkey/core';

/** What the `rating` remote exposes as `./Rating`. */
type RatingModule = { Rating: Type<unknown> };

@Component({
  selector: 'app-remote-rating',
  template: `
    @if (rating.error()) {
    <p class="remote-rating-unavailable">The rating is currently not available.</p>
    } @else if (rating.isLoading()) {
    <p>Loading rating...</p>
    }
    <ng-container #host />
  `
})
export class RemoteRating {
  private readonly loadRemoteModule = inject(REMOTE_MODULE_LOADER);
  private readonly host = viewChild.required('host', { read: ViewContainerRef });

  readonly value = model(0);

  protected readonly rating = resource({
    loader: () => this.loadRemoteModule<RatingModule>('rating', './Rating')
  });
}
```

---

- **Render the loaded component** `twoWayBinding(){:ts}` accepts every writable signal, also a `model(){:ts}`. Without the `hasValue(){:ts}` guard the `effect(){:ts}` throws when the remote is down.

```ts
// libs/feature-books/src/lib/remote-rating/remote-rating.ts (inside the class)
constructor() {
  effect(() => {
    const remote = this.rating.hasValue() ? this.rating.value() : undefined;
    if (remote) {
      this.host().createComponent(remote.Rating, {
        bindings: [twoWayBinding('value', this.value)]
      });
    }
  });
}
```

---

- **Use it on the detail page**

```ts
// libs/feature-books/src/lib/book-detail-page/book-detail-page.ts
imports: [RouterLink, BookDetail, RemoteRating],
// ...
protected readonly rating = signal(0);
```

```html
<!-- libs/feature-books/src/lib/book-detail-page/book-detail-page.html -->
<app-book-detail [book]="book" />
<section class="book-detail-rating">
  <h2>Your rating</h2>
  <app-remote-rating [(value)]="rating" />
</section>
```

---

- **Repair the existing test** A promise that never settles keeps the resource in the loading state.

```ts
// libs/feature-books/src/lib/book-navigation.spec.ts (in the providers of the TestBed)
{ provide: REMOTE_MODULE_LOADER, useValue: () => new Promise<never>(() => undefined) }
```

```ts
// libs/feature-books/src/lib/remote-rating/remote-rating.spec.ts
import { TestBed } from '@angular/core/testing';
import { REMOTE_MODULE_LOADER } from '@bookmonkey/core';
import { RemoteRating } from './remote-rating';

describe('RemoteRating', () => {
  it('shows a hint when the remote cannot be loaded', async () => {
    TestBed.configureTestingModule({
      providers: [
        { provide: REMOTE_MODULE_LOADER, useValue: () => Promise.reject(new Error('remote is down')) }
      ]
    });

    const fixture = TestBed.createComponent(RemoteRating);
    await fixture.whenStable();

    expect(fixture.nativeElement.textContent).toContain('currently not available');
  });
});
```
