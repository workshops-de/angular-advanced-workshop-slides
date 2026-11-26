- **Create the spec file** and install the time machine. The fake clock must be active before the component is rendered, because the timer starts in the constructor of `App{:ts}`.

```ts
// apps/bookmonkey-client/src/app/app-greeting.spec.ts
import { provideRouter } from '@angular/router';
import { render } from 'vitest-browser-angular';

import { App } from './app';

describe('App greeting', () => {
  beforeEach(() => {
    vi.useFakeTimers();
  });

  afterEach(() => {
    vi.useRealTimers();
  });
});
```

---

- **Render the app** Add `provideRouter([]){:ts}`, because the app contains a router outlet.

```ts
const screen = await render(App, { providers: [provideRouter([])] });
```

---

- **Travel in time** `advanceTimersByTimeAsync(){:ts}` moves the fake clock and lets pending promises settle.

```ts
it('greets the attendee after 6 seconds', async () => {
  const screen = await render(App, { providers: [provideRouter([])] });
  const heading = screen.getByRole('heading', { level: 1 });

  await expect.element(heading).not.toHaveTextContent('Hello');

  await vi.advanceTimersByTimeAsync(5999);
  await expect.element(heading).not.toHaveTextContent('Hello');

  await vi.advanceTimersByTimeAsync(1);
  await expect.element(heading).toHaveTextContent('Hello Angularian');
});
```
