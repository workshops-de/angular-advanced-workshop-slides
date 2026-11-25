- **Create the Web Component** The injector of the created application is what the custom element needs to resolve dependencies.

```ts
// apps/rating/src/app/rating/rating-element.ts
import { provideBrowserGlobalErrorListeners } from '@angular/core';
import { createCustomElement } from '@angular/elements';
import { createApplication } from '@angular/platform-browser';
import { Rating } from './rating';

createApplication({ providers: [provideBrowserGlobalErrorListeners()] })
  .then(({ injector }) => {
    if (!customElements.get('rating-stars-element')) {
      customElements.define('rating-stars-element', createCustomElement(Rating, { injector }));
    }
  })
  .catch(err => console.error(err));
```

Inputs of the component become properties of the element (`value`, `max`, `readonly`). The `value` model additionally becomes the event `valueChange`.

---

- **Expose the element** and **Stop sharing Angular** Regular expressions in `skip{:ts}` match whole families of packages.

```js
// apps/rating/federation.config.mjs
export default withNativeFederation({
  name: 'rating',

  exposes: {
    './RatingElement': './apps/rating/src/app/rating/rating-element.ts'
  },

  // shared: ... stays as it is

  skip: [
    'rxjs/ajax',
    'rxjs/fetch',
    'rxjs/testing',
    'rxjs/webSocket',
    /^@angular\//,
    /^rxjs/
  ]
});
```

Restart `nx serve rating{:bash}` after changing the config. Only `tslib` stays in the `shared` section of `remoteEntry.json`.

---

- **Use the tag in the host** The resource only waits until the module is loaded, so it resolves to a plain `true{:ts}`. The event handler receives an `Event{:ts}` and casts it to a `CustomEvent{:ts}`.

```ts
// libs/feature-books/src/lib/remote-rating/remote-rating.ts
import { CUSTOM_ELEMENTS_SCHEMA, Component, inject, model, resource } from '@angular/core';
import { REMOTE_MODULE_LOADER } from '@bookmonkey/core';

@Component({
  selector: 'app-remote-rating',
  schemas: [CUSTOM_ELEMENTS_SCHEMA],
  template: `
    @if (rating.hasValue()) {
    <rating-stars-element [value]="value()" (valueChange)="onValueChange($event)" />
    } @else if (rating.error()) {
    <p class="remote-rating-unavailable">The rating is currently not available.</p>
    } @else {
    <p>Loading rating...</p>
    }
  `
})
export class RemoteRating {
  private readonly loadRemoteModule = inject(REMOTE_MODULE_LOADER);

  readonly value = model(0);

  protected readonly rating = resource({
    loader: async () => {
      await this.loadRemoteModule('rating', './RatingElement');
      return true;
    }
  });

  protected onValueChange(event: Event): void {
    this.value.set((event as CustomEvent<number>).detail);
  }
}
```

---

- **Test and check** In a unit test nobody registers the tag, so the element stays a plain `HTMLElement{:ts}`. That is enough to check the wiring of the host.

```ts
// libs/feature-books/src/lib/remote-rating/remote-rating.spec.ts
function create(loader: () => Promise<unknown>) {
  TestBed.configureTestingModule({
    providers: [{ provide: REMOTE_MODULE_LOADER, useValue: loader }]
  });
  return TestBed.createComponent(RemoteRating);
}

it('renders the custom element of the remote', async () => {
  const fixture = create(() => Promise.resolve({}));
  await fixture.whenStable();

  expect(fixture.nativeElement.querySelector('rating-stars-element')).not.toBeNull();
});

it('takes over the value emitted by the custom element', async () => {
  const fixture = create(() => Promise.resolve({}));
  await fixture.whenStable();

  const element: HTMLElement = fixture.nativeElement.querySelector('rating-stars-element');
  element.dispatchEvent(new CustomEvent('valueChange', { detail: 4 }));

  expect(fixture.componentInstance.value()).toBe(4);
});
```

Do not run a production build (`nx build{:bash}`) while `nx serve{:bash}` is running. Both share the federation cache, and the dev server then fails with _ngDevMode is not defined_. Restart the servers to fix it.
