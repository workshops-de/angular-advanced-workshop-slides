Some code depends on **time**: the `App{:ts}` (`apps/bookmonkey-client/src/app/app.ts`) greets the attendee with a `setTimeout(){:ts}`. After 6 seconds the heading changes from _Angularian_ to _Hello Angularian_. You do not want to wait 6 seconds in a test. Build a **time machine** with Vitest fake timers and travel forward instantly.

---

- **Create the spec file** `apps/bookmonkey-client/src/app/app-greeting.spec.ts` with a `describe('App greeting', ...){:ts}` block.

---

- **Install the time machine** Fake the clock before each test and restore it afterwards:
  - `vi.useFakeTimers(){:ts}` in a `beforeEach(){:ts}`.
  - `vi.useRealTimers(){:ts}` in an `afterEach(){:ts}`.

---

- **Render the app** `const screen = await render(App, { providers: [provideRouter([])] }){:ts}` (`render{:ts}` from `vitest-browser-angular`). The timer starts in the constructor, so the fake clock must be installed before rendering.

---

- **Travel in time** `it('greets the attendee after 6 seconds', ...){:ts}`:
  - Assert that the `h1{:html}` heading shows _Angularian_ without _Hello_.
  - Jump to just before the deadline with `await vi.advanceTimersByTimeAsync(5999){:ts}`. The heading must not change yet.
  - Travel the last millisecond with `await vi.advanceTimersByTimeAsync(1){:ts}`.
  - Assert with `expect.element(screen.getByRole('heading', { level: 1 })).toHaveTextContent('Hello Angularian'){:ts}`.

Run `npx nx test bookmonkey-client{:bash}`.
