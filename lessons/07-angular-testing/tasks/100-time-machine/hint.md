- **Install the time machine** Write a helper that fakes the clock and restores the real one when the test is over. `onTestFinished(){:ts}` runs even if the test fails, so the fake clock never leaks into other tests. Call the helper **before** rendering, because the timer starts in the constructor of `App{:ts}`.

```ts
// apps/bookmonkey-client/src/app/app-greeting.spec.ts
import { provideRouter } from '@angular/router';
import { onTestFinished } from 'vitest';
import { render } from 'vitest-browser-angular';

import { App } from './app';

function setUpTimeMachine() {
  vi.useFakeTimers();
  onTestFinished(() => {
    vi.useRealTimers();
  });
}
```

---

- **Travel in time** `advanceTimersByTimeAsync(){:ts}` moves the fake clock and lets pending promises settle. Add `provideRouter([]){:ts}`, because the app contains a router outlet.

```ts
describe('App greeting', () => {
  it('greets the attendee after 6 seconds', async () => {
    setUpTimeMachine();

    const screen = await render(App, { providers: [provideRouter([])] });
    const heading = screen.getByRole('heading', { level: 1 });

    await expect.element(heading).not.toHaveTextContent('Hello');

    await vi.advanceTimersByTimeAsync(5999);
    await expect.element(heading).not.toHaveTextContent('Hello');

    await vi.advanceTimersByTimeAsync(1);
    await expect.element(heading).toHaveTextContent('Hello Angularian');
  });
});
```

---

- **Skip the waiting** With `nextTimerAsync{:ts}` the fake clock jumps to the next pending timer whenever the test awaits. You do not need to know the delay. Restore the real clock again with `onTestFinished(){:ts}`.

```ts
function setUpFastForward() {
  vi.useFakeTimers().setTimerTickMode('nextTimerAsync');
  onTestFinished(() => {
    vi.useRealTimers();
  });
}

it('greets the attendee without guessing the time', async () => {
  setUpFastForward();

  const screen = await render(App, { providers: [provideRouter([])] });

  await expect.element(screen.getByRole('heading', { level: 1 })).toHaveTextContent('Hello Angularian');
});
```
