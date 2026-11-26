Some code depends on **time**: the `App{:ts}` (`apps/bookmonkey-client/src/app/app.ts`) greets the attendee with a `setTimeout(){:ts}`. After 6 seconds the heading changes from _Angularian_ to _Hello Angularian_. You do not want to wait 6 seconds in a test. Build a **time machine** with Vitest fake timers and travel forward instantly.

---

- **Create the spec file** `apps/bookmonkey-client/src/app/app-greeting.spec.ts` with a `describe('App greeting', ...){:ts}` block.

---

- **Install the time machine** Write a helper `setUpTimeMachine(){:ts}` and call it at the start of each test:
  - It fakes the clock with `vi.useFakeTimers(){:ts}`.
  - It restores the real clock when the test is over: `onTestFinished(() => vi.useRealTimers()){:ts}` (import from `vitest`). Otherwise the fake clock leaks into other tests.

---

- **Render the app** `const screen = await render(App, { providers: [provideRouter([])] }){:ts}` (`render{:ts}` from `vitest-browser-angular`). The timer starts in the constructor, so the fake clock must be installed before rendering.

---

- **Travel in time** `it('greets the attendee after 6 seconds', ...){:ts}`:
  - Assert that the `h1{:html}` heading shows _Angularian_ without _Hello_.
  - Jump to just before the deadline with `await vi.advanceTimersByTimeAsync(5999){:ts}`. The heading must not change yet.
  - Travel the last millisecond with `await vi.advanceTimersByTimeAsync(1){:ts}`.
  - Assert with `expect.element(screen.getByRole('heading', { level: 1 })).toHaveTextContent('Hello Angularian'){:ts}`.

---

- **Skip the waiting** Guessing `6000{:ts}` is brittle: if the delay changes, the test breaks. Let the clock jump to the next timer by itself. Add `it('greets the attendee without guessing the time', ...){:ts}`:
  - Write a second helper `setUpFastForward(){:ts}`: fake the clock, call `vi.setTimerTickMode('nextTimerAsync'){:ts}` and restore the real clock with `onTestFinished(){:ts}` again. Call it **before** rendering.
  - Render the app and assert with `expect.element(...).toHaveTextContent('Hello Angularian'){:ts}`. No `advanceTimersByTimeAsync(){:ts}` needed.
  - This also keeps tests with long animations or delays stable and fast.

Run `npx nx test bookmonkey-client{:bash}`.
