# Lesson 05: Testing, Recherche und Referenzmaterial

> Ausgelagert aus [05-testing.md](./05-testing.md). Hier liegen Quellen, Befunde und Referenzcode. **Entscheidungen stehen nur im Hauptplan.** Alles hier ist aus Doku oder Code gelesen und **nicht ausgeführt**, solange nichts anderes dasteht.


## 1. Grenzen von TestBed (Folienmaterial)

Quellen nur aus der Angular-Doku. Eine weitere Quelle (Video) gibt es nicht.

| Grenze                                     | Konkret                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Vieles muss explizit gemacht werden        | `configureTestingModule`, Fixture, Inputs per `componentRef.setInput`, Provider, alles von Hand                                                                                                                                                                                                                                                                                                                                        |
| `detectChanges()` muss man selbst aufrufen | Wer es vergisst, sieht kein oder ein veraltetes DOM. Fehlerbild ist schwer zu erkennen.                                                                                                                                                                                                                                                                                                                                                |
| `whenStable()` hat Grenzen                 | Löst auf, wenn die Fixture stabil ist, also keine von Angular verfolgten Async-Tasks mehr laufen. Alles andere (z. B. Fake-Timer, externe Async-Arbeit) ist nicht erfasst. **Keine weitere Quelle, auf der Folie nur die dokumentierten Fakten zeigen.**                                                                                                                                                                                                |
| Zone vs. Zoneless ändert die Regeln        | Auto-Detect ist laut Angular-Doku bei Zoneless standardmäßig an, bei Zone-basierten Tests aus. Die Zoneless-Doku empfiehlt `await fixture.whenStable()` statt `detectChanges()`. Einmal aktiviertes Auto-Detect lässt sich bei Zoneless nicht mehr abschalten (laut Commit, nicht gegen Release geprüft). Quellen: [Utility APIs](https://angular.dev/guide/testing/utility-apis), [Zoneless](https://v20.angular.dev/guide/zoneless). |
| DOM-Zugriff ist wenig ergonomisch          | `querySelector` und `textContent` koppeln den Test an die Markup-Struktur, nicht an das Nutzerverhalten                                                                                                                                                                                                                                                                                                                                |
| Asynchrone Zustände                        | Warten, Polling und Retry muss man selbst bauen                                                                                                                                                                                                                                                                                                                                                                                        |

## 2. `expect.poll`

- Konfiguration laut [Vitest `expect`-Config](https://vitest.dev/config/expect#expect-poll): `expect.poll.interval` (Standard 50 ms) und `expect.poll.timeout` (Standard 1000 ms). Dieselben Optionen lassen sich pro Aufruf mit `expect.poll(condition, options)` überschreiben. Die Seite `browser.expect` wurde nicht gelesen.
- `expect.poll` laut [Vitest API](https://vitest.dev/api/expect#poll) (Seite war gekürzt, der Poll-Abschnitt lag im gelesenen Teil): `expect.poll(() => wert, { interval, timeout, message })` wiederholt die Assertion bis sie passt. Der Callback bekommt ein `AbortSignal`. Wirft er, wird bis zum Timeout wiederholt. **Immer `await`**, sonst schlägt der Test seit Vitest 3 mit Warnung fehl. Nicht unterstützt: Snapshot-Matcher, `.resolves`/`.rejects`, `toThrow`. Mit `vi.useFakeTimers` ruft Vitest nach jedem Fehlversuch `vi.advanceTimersByTime(interval)` auf (Anknüpfung für die Folie zu `whenStable` und Timern).

## 3. Browser Mode: Quellen und Argumente

- **Problem isolierter Tests:** Sie entsprechen nicht der Realität. Gegen jsdom fehlen Browser-APIs. Tests können grün sein und im echten Browser trotzdem fehlschlagen, weil die Umgebung nicht dieselbe ist.
- **Der Trend:** Tests wandern seit Langem immer mehr in den Browser. Früher waren Browser-Läufe sehr langsam, die Ausführung dauerte lange.
- **Was sich geändert hat:** Browser-Tests werden allgemein immer schneller. Vitest bindet die Browser-APIs als Plattform so geschickt ein, dass man sie ohne große Performance-Bedenken nutzen kann. Gemeint ist die Entwicklung von Browser-Tests insgesamt, **kein Vergleich Browser gegen jsdom**.
- Dieselbe Linie nennt die Vitest-Doku ("Why Browser Mode", siehe oben). Die Aussage zur Geschwindigkeit ist Gregors Einschätzung und **nicht gemessen**. Eine Messung (Zeit je Lauf im Browser Mode) kann bei Bedarf eine Zahl für die Folie liefern.
- Gegenargument ehrlich nennen: Start von Provider und Browser kostet Zeit, und Browser Mode ersetzt keinen E2E-Runner.


- Locators: <https://vitest.dev/api/browser/locators>
- Assertions: <https://vitest.dev/api/browser/assertions>
- Beide Seiten sind Pflichtlektüre für Task 40. Die Assertions-Seite wurde bereits teilweise gelesen (siehe `expect.element` unten), die Locators-Seite noch nicht. Vor dem Schreiben von Task 40 lesen.

- Derselbe Test läuft im echten Browser (Playwright-Provider).
- Fragen: Was ändert sich bei der Konfiguration? Was ist gleich geblieben? Welche Fehler findet nur der Browser (Layout, echte Events)?
- Konfiguration laut [Vitest-Doku](https://vitest.dev/guide/browser/) (Seite zu v5.1.1 gelesen): `test.browser.enabled: true`, `provider: playwright()` (empfohlen, Paket `@vitest/browser-playwright`), `instances: [{ browser: 'chromium' }]`, `headless`. Ein Provider ist Pflicht. Für Angular gibt es kein offizielles Plugin, die Doku verlinkt `vitest-browser-angular`.
- **Warum Browser Mode (Folienargument)**, laut [Vitest "Why Browser Mode"](https://vitest.dev/guide/browser/why): jsdom und happy-dom simulieren den Browser nur, dadurch sind falsch positive und falsch negative Ergebnisse möglich. Nachteile: längere Initialisierung (Provider und Browser starten) und kein Ersatz für E2E-Runner. Die Doku empfiehlt, Browser Mode um einen eigenen Runner wie Playwright zu ergänzen. Das passt zu Task 100.

- Bekannte Einschränkungen: `alert`/`confirm` blockieren (Vitest mockt sie), `vi.spyOn` auf Modul-Exports wirft (Workaround `vi.mock(pfad, { spy: true })`).
- `expect.element` laut [Browser-Assertions](https://vitest.dev/api/browser/assertions) (Seite gekürzt, Matcher-Liste am Ende nur zusammengefasst): "Like expect.poll, expect.element retries DOM assertions until they pass or the timeout is reached." Locators werden vor der Assertion per `locator.findElement()` aufgelöst. Empfehlung der Doku: bei `page.getBy*` immer `expect.element` nutzen. Ein einfaches `expect(locator).toX()` wiederholt **nicht**. `timeout` und `interval` übernehmen die Werte aus `expect.poll.timeout` und `expect.poll.interval`. Die Matcher stammen aus `@testing-library/jest-dom`, erweitert um Locators und Retry. Nützlich für die Aufgabe: `toBeVisible`, `toBeEnabled`, `toBeInTheDocument`, `toHaveTextContent`, `toHaveValue`, `toHaveAccessibleName`, `toHaveAttribute`, `toHaveClass`.
- **Offen nur für `expect.element`:** Die Doku formuliert es browser-bezogen (Locators kommen aus `vitest/browser`). Dass es ohne Browser Mode läuft, ist nicht belegt und unwahrscheinlich. `expect.poll` ist davon getrennt (siehe Task 20).
- `expect.element` mit Locators (`page.getBy*`) ist die Kernneuheit hier, mit automatischem Retry.
- Angular-Material dazu: [Ninja Squad: Angular tests with Vitest browser mode](https://blog.ninja-squad.com/2025/11/18/angular-tests-with-vitest-browser-mode), Community-Paket `vitest-browser-angular` (Reife prüfen, Alternative zu `TestBed`-Rendering).
- Brücke: Lesson 03 hat Browser-Tests gegen Stories vorgewärmt. Playwright-Browser sind vorab installiert (Preparation).

## 4. Locators und Assertions (Vitest-Doku v5.1.1)

### Geprüfte Locator-Details (Vitest-Doku v5.1.1, 2026-11-24)

Quellen: [Locators](https://vitest.dev/api/browser/locators) (vollständig gelesen), [Assertions](https://vitest.dev/api/browser/assertions) (etwa drei Viertel gelesen, Rest nur als Zusammenfassung).

| Frage | Ergebnis |
| ----- | -------- |
| `nth()`, `first()`, `last()` | **Ja.** `nth(index)` ist nullbasiert und wird **wiederholt**, bis das Element da ist. `first()` = `nth(0)`, `last()` = `nth(-1)`. |
| `click()`, `fill()` | **Ja.** `fill(text)` setzt den Wert von `input`, `textarea` oder `contenteditable`. Alle Locator-Methoden sind async, ohne `await` schlägt der Test seit Vitest 3 fehl. |
| `getByTestId()` | **Ja.** Attribut konfigurierbar über `browser.locators.testIdAttribute`. Der Standardwert steht in der gelesenen Doku **nicht**, vermutlich `data-testid` (nicht belegt, beim Schreiben der Tests prüfen). Die Doku rät, es erst zu nutzen, wenn andere Locators nicht passen. |
| `toHaveLength` auf einem Locator | **Nicht dokumentiert.** Das Beispiel von Gregor nutzt es, die Doku kennt keinen Zähl-Matcher. Siehe unten. |
| Placeholder im Accessible Name | **Nicht dokumentiert.** Placeholder hat einen eigenen Locator `getByPlaceholder`. Die Doku bevorzugt `getByLabelText`. |
| Mehrfachtreffer | `.element()`, `.query()` und `expect.element` sind **strikt** und werfen bei mehreren Treffern. Für `click`/`fill` ist es nicht ausdrücklich beschrieben, die Doku deutet aber einen Fehler an. Deshalb im Beispiel `.first()`. |
| Matcher in `expect.element` | `toBeDisabled`, `toBeEnabled`, `toBeEmptyDOMElement`, `toBeInTheDocument`, `toBeInvalid`, `toBeRequired`, `toBeValid`, `toBeVisible`, `toBeInViewport`, `toContainElement`, `toContainHTML`, `toHaveAccessibleDescription`, `toHaveAccessibleErrorMessage`, `toHaveAccessibleName`, `toHaveAttribute`, `toHaveClass`, `toHaveFocus`, `toHaveFormValues`, `toHaveStyle`, `toHaveTextContent`, `toMatchTextContent`, `toHaveValue`, `toHaveDisplayValue`, `toBeChecked`, `toBePartiallyChecked`. Zusätzlich `toHaveRole`, `toHaveSelection` und `toMatchScreenshot` (laut Gregor, 2026-11-24, über die Anker [#tohaverole](https://vitest.dev/api/browser/assertions#tohaverole), [#tohaveselection](https://vitest.dev/api/browser/assertions#tohaveselection), [#tomatchscreenshot](https://vitest.dev/api/browser/assertions#tomatchscreenshot) bestätigt, nur `toMatchScreenshot` ist noch experimentell, `toHaveRole` und `toHaveSelection` nicht). |
| Namensabgleich | Die Doku widerspricht sich (exakt per Standard `browser.locators.exact: true` oder Substring ohne Groß-/Kleinschreibung). Beim Schreiben der Tests prüfen. |

**Zählen von Elementen** (Vorschlag, die Einzelteile sind belegt, die Kombination wurde nicht ausgeführt):

```ts
// mit Retry, empfohlen
await expect.poll(() => cookbooks.elements().length).toBe(3);

// ohne Retry
expect(cookbooks.elements()).toHaveLength(3);
```

`locator.length` ist ein Getter ohne Retry, `elements()` wirft nie und liefert ein leeres Array. Das Beispiel mit `expect.element(cookbooks).toHaveLength(3)` ist daher **vor der Verwendung in Folien zu prüfen**. In den Folien lieber die belegte Variante mit `expect.poll` zeigen, das knüpft außerdem an Task 20 an.

## 5. Cookbook-Beispiel (nur für den Agenten, der die Tests baut, nicht für die Folien)

Quelle nicht genannt. Der Code ist nur Muster für das Buch-Beispiel.

```ts
import { TestBed } from '@angular/core/testing';
import { describe, expect, it } from 'vitest';
import { page } from 'vitest/browser';
import { Cart } from './cart';
import { CookbookSearch } from './cookbook-search.ng';

describe(CookbookSearch.name, () => {
  it('filters recipes by authors name', async () => {
    TestBed.createComponent(CookbookSearch);

    await page.getByRole('textbox', { name: 'Keywords' }).fill('Marmicode');

    const cookbooks = page.getByRole('heading');
    await expect.element(cookbooks).toHaveLength(3);
    await expect
      .element(cookbooks.nth(0))
      .toHaveTextContent('Angular Testing Cookbook');
    await expect
      .element(cookbooks.nth(1))
      .toHaveTextContent('Angular Core Cookbook');
    await expect.element(cookbooks.nth(2)).toHaveTextContent('Nx Cookbook');
  });

  it('adds first cookbook (Angular Testing Cookbook) to the cart', async () => {
    const cart = TestBed.inject(Cart);
    TestBed.createComponent(CookbookSearch);

    await page.getByRole('button', { name: 'Add to Cart' }).first().click();

    expect(cart.cookbooks()).toMatchObject([
      { title: 'Angular Testing Cookbook' }
    ]);
  });
});
```

### Was das Beispiel zeigt (aus dem Code gelesen, nicht ausgeführt)

| Beobachtung                                                                                                                       | Folge für den Workshop                                                                                                                                                                                                                   |
| --------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Rendering nur mit `TestBed.createComponent(...)`, **kein** `configureTestingModule`, **kein** `detectChanges`, **kein** `fixture` | Beantwortet die offene Rendering-Frage: Für die Folie reicht `TestBed`, `vitest-browser-angular` ist nicht nötig. Das passt zur Kritik an der TestBed-API (Folie "Grenzen"): Der Ballast verschwindet, weil Locators automatisch warten. |
| `page` kommt aus `vitest/browser`, nicht aus einer Testing-Library-Importquelle                                                   | Task 40: `page.getByRole(...)` ersetzt `screen.getBy...`.                                                                                                                                                                                |
| `fill(...)` und `click()` direkt am Locator, beide mit `await`                                                                    | Ersetzt `userEvent`. Folgt dem Locator-Prinzip aus der Locators-Doku.                                                                                                                                                                    |
| `expect.element(...)` für alle DOM-Assertions, mit Retry                                                                          | Kein manuelles Warten. Gegenüber Task 10 fallen `detectChanges` und `querySelector` weg.                                                                                                                                                 |
| `cookbooks.nth(0)` und `.first()`                                                                                                 | Mehrere Treffer werden über Locator-Methoden angesprochen, nicht über Array-Indizes.                                                                                                                                                     |
| Die zweite Assertion nutzt ein normales `expect(...)` auf einem Signal (`cart.cookbooks()`)                                       | Bewusste Unterscheidung: `expect.element` für DOM mit Retry, `expect` für Zustand, der nach `await click()` schon gesetzt ist. Als Merksatz auf die Folie.                                                                               |
| `TestBed.inject(Cart)` vor `createComponent`                                                                                      | Dieselbe Instanz kommt vom Injector, kein Mock nötig. Das ist **Integration** (echter Service), nicht Shallow.                                                                                                                           |
| `describe(CookbookSearch.name, ...)`                                                                                              | Kleines Muster: Klassenname statt String, refactoring-sicher.                                                                                                                                                                            |
| Kein Import von `@testing-library/*`                                                                                              | Bestätigt die Entscheidung, die Testing Library zu streichen.                                                                                                                                                                            |

## 6. Übertragung auf BookMonkey

Bestehende Elemente in `libs/feature-books` (gelesen):

- `BooksPage` mit `<input type="search" placeholder="Search...">`, einer Liste von `app-book-card` und je Karte einem `Delete`-Button.
- `BookCard` mit `h3` (Titel) und `h4` (Autor).
- Das Suchfeld **filtert und markiert** (Korrektur durch Gregor, 2026-11-24, im Code bestätigt): `BooksPage.booksComputed` filtert die Bücher per `bookMatches(book, markTerm)`, die Direktive `appBookMarker` hebt den Treffer zusätzlich hervor. Ein Test "filtert nach Suchbegriff" lässt sich also direkt vom Cookbook-Beispiel übernehmen.
- `deleteBook` ruft `window.confirm(...)` auf. Vitest mockt `confirm` im Browser Mode (siehe Abschnitt 3). Für den Delete-Test muss das Verhalten bewusst gesteuert werden (z. B. `vi.spyOn(window, 'confirm')`, beim Schreiben der Tests prüfen).

| Cookbook-Beispiel                            | Buch-Variante                                                                                                                                                                                                                                   |
| -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Rezepte nach Autor filtern | Suchbegriff eingeben, nur Karten mit Treffer bleiben sichtbar (Titel wird zusätzlich markiert) |
| `getByRole('textbox', { name: 'Keywords' })` | Das Suchfeld hat nur `placeholder`, kein Label. Die Vitest-Doku sagt nichts dazu, ob der Placeholder in den Namen von `getByRole` einfließt, und rät zu `getByLabelText` statt Placeholder. **Entscheidung (Gregor, 2026-11-24):** `aria-label` ergänzen und `getByRole('searchbox', { name: 'Search' })` nutzen. `getByTestId` nur als Hinweis auf der Folie. |
| `getByRole('heading')` + `toHaveLength(3)` | `getByRole('heading', { level: 3 })` für die Buchtitel. Zählen siehe "Geprüfte Locator-Details" oben. |
| `Add to Cart` + `Cart`                       | `Delete`-Button auf der Karte + `BooksClient`/Store. Der Test prüft, dass das Buch verschwindet. Dafür muss HTTP gemockt werden (Integration, Task 70).                                                                                         |
| `TestBed.inject(Cart)`                       | `TestBed.inject(BookMarkerStore)` für den Marker-Zustand (existiert schon im `BookCard`-Spec)                                                                                                                                                   |

## 7. Router testen

Quelle: [Angular: Testing routing](https://angular.dev/guide/routing/testing) (Seite v22.3.0, gelesen, nicht ausgeführt). **Der Name ist `RouterTestingHarness`**, nicht `RouterTestHarness`. Import: `import { RouterTestingHarness } from '@angular/router/testing';`.

| Thema | Laut Doku |
| ----- | --------- |
| Setup | `TestBed.configureTestingModule({ imports: [Komponente], providers: [provideRouter([...routes])] })`, dann `harness = await RouterTestingHarness.create()` in `beforeEach` |
| Navigieren | `await harness.navigateByUrl('/user/123', UserProfile)`. Das zweite Argument ist der Komponententyp, auf den gewartet wird. Der Rückgabewert ist die Komponenteninstanz. Alternative: `harness.navigate([...commands], extras, Komponente)` |
| Rendering prüfen | `harness.routeNativeElement?.textContent` |
| URL prüfen | `TestBed.inject(Router).url` |
| Router mocken? | Nein. Die Doku rät dazu, echte Route-Configs und die Harness zu nutzen |
| Async | Navigation ist asynchron, immer `await` |
| Test-Runner | Die Doku nennt Vitest als Voraussetzung |

**Nicht in der Quelle (selbst klären, beim Schreiben der Tests):**
- **Klicks:** Es gibt kein Beispiel, das auf einen `routerLink` klickt. Für unseren Task ("Klick auf Buchkarte") muss das Muster selbst gebaut werden, vermutlich `harness.navigateByUrl('/books')`, dann `page.getByRole(...).click()` (Locator aus Task 40) und danach `expect.poll(() => TestBed.inject(Router).url)`. Nicht verifiziert.
- Zoneless, `detectChanges`, `whenStable`: nicht erwähnt. Wie sich die Harness im Zoneless-Setup und im Browser Mode verhält, ist offen.
- Ob `routeNativeElement` mit den Locators (`page.getBy*`) zusammenspielt, ist offen. Locators suchen im Dokument, die Harness hängt die Fixture aber vermutlich selbst ein (Annahme, prüfen).
- `routeDebugElement` kommt in der Doku nicht vor.

## 8. Referenz: Zweiprojekt-Config (emulated/browser), **wird nicht gelehrt**

Entscheidung: Der Workshop läuft komplett im Browser Mode. Diese Config von Gregor (aus einem anderen Projekt, Pfade passen nicht) bleibt nur als Referenz.

```ts
import { defineConfig } from 'vitest/config';
import viteConfig from './vite.config.mjs';
import { playwright } from '@vitest/browser-playwright';
import { preview } from '@vitest/browser-preview';

const emulatedTestPatterns = ['src/**/!(*.browser).spec.ts'];
const browserTestPatterns = ['src/**/*.browser.spec.ts'];

export default defineConfig({
  ...viteConfig,
  test: {
    watch: false,
    include: [],
    reporters: ['default'],
    coverage: {
      reportsDirectory: '../../coverage/apps/demo',
      provider: 'v8' as const
    },
    setupFiles: ['src/test-setup.ts'],
    testTimeout: 1_500,
    projects: [
      {
        extends: true,
        test: {
          name: 'emulated',
          environment: 'jsdom',
          include: emulatedTestPatterns,
          setupFiles: ['@testing-library/jest-dom/vitest']
        }
      },
      {
        extends: true,
        test: {
          name: 'browser',
          include: browserTestPatterns,
          browser: {
            enabled: true,
            provider: playwright(),
            instances: [{ browser: 'chromium' }]
          }
        }
      }
    ]
  }
});
```

Was die Config zeigt (aus dem Text gelesen, nicht ausgeführt):

| Element                                          | Bedeutung                                                                                                                    |
| ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------- |
| `*.spec.ts` ohne `.browser`                      | Läuft im Projekt `emulated` mit `environment: 'jsdom'`                                                                       |
| `*.browser.spec.ts`                              | Läuft im Projekt `browser` mit Playwright und Chromium                                                                       |
| `extends: true`                                  | Beide Projekte erben die Basis (`viteConfig`, `setupFiles`, `testTimeout`)                                                   |
| `include: []` auf Root-Ebene                     | Root führt selbst keine Tests aus, nur die Projekte                                                                          |
| `isStackblitz ? preview() : playwright()`        | In StackBlitz (WebContainer) gibt es kein Playwright. Der Provider `preview()` aus `@vitest/browser-preview` ist der Ersatz. |
| `testTimeout: 1_500`                             | Kurzer Timeout, passt zu schnellen Tests                                                                                     |
| `@testing-library/jest-dom/vitest` in `emulated` | Matcher nur für jsdom-Tests. Browser-Tests bekommen sie über `expect.element`.                                               |

## 9. ArchUnitTS

Genannt wurde "ArcUnit", gemeint ist vermutlich ArchUnit. Für TypeScript kommt **ArchUnitTS** in Frage (npm-Paket `archunit`). Das Paket ist im Workshop-Repo **vorinstalliert** (E6).

Stand der Recherche (README gelesen, **nicht ausprobiert**):

- ArchUnitTS ist **kein offizieller Port** von ArchUnit, sondern eine eigenständige, davon inspirierte Bibliothek.
- Installation: `npm install archunit --save-dev`.
- Funktioniert mit Jest, Vitest, Jasmine und anderen Runnern. Für Vitest nennt die README `globals: true` in der `vitest.config.ts`. Der Matcher heißt `toPassAsync()`.
- Beispiele aus der README: `projectFiles().inFolder('src/presentation/**').shouldNot().dependOnFiles().inFolder('src/database/**')` und `projectFiles().inFolder('src/**').should().haveNoCycles()`. Außerdem Namensregeln und Metriken (`metrics().count().linesOfCode().shouldBeBelow(1000)`).
- **Unklar:** Umgang mit Pfad-Aliasen (`@...`), die Angular und Nx nutzen. Die README sagt dazu nichts im gelesenen Teil. Es gibt ein Angular-Beispiel im Repo (nicht gelesen). Die Nx-Anbindung ist nicht verifiziert.

Nx Boundaries (Lesson 02) kontrollieren Abhängigkeiten **zwischen Projekten** per Tags. Architekturtests sollen das **ergänzen**, nicht doppeln:

| Ebene                    | Nx Boundaries | Architekturtest |
| ------------------------ | ------------- | --------------- |
| Zwischen Libraries       | ja            | ja (überlappt, siehe Task 90) |
| Innerhalb einer Library  | nein          | ja              |
| Namens- und Ordnerregeln | nein          | ja              |
| Zyklen                   | teilweise     | ja              |
| Umfang/Metriken          | nein          | ja              |

### Kandidaten für günstige, gute Tests

Kriterium: wenig Aufwand, aussagekräftig, kaum Fehlalarme.

| Regel                                                               | Warum gut                                                 | Aufwand |
| ------------------------------------------------------------------- | --------------------------------------------------------- | ------- |
| Keine Zyklen innerhalb von Features (`haveNoCycles`)                | Zyklen sind ein klassischer Anti-Pattern-Fund             | niedrig |
| `HttpClient` nur in `data-access`, nie in Komponenten               | schützt die Schichtentrennung, sofort verständlich        | niedrig |
| Features importieren sich innerhalb einer Library nicht gegenseitig | hält Feature-Slices sauber                                | niedrig |
| Namenskonvention (`*.store.ts` in `data-access`, `*-api-client.ts`) | zeigt Namens-Regeln, günstig                              | niedrig |
| Dateigröße oder Zeilenzahl unter Grenzwert                          | zeigt Metriken, allerdings oft Fehlalarme bei Grenzwerten | niedrig |

## 10. Playwright mit Nx einrichten

**Ausgangslage im `sample-solution`:** Es gibt kein Playwright-Setup. In `package.json` stehen nur `playwright` und `@vitest/browser-playwright` (für den Browser Mode), `@nx/playwright` und `@playwright/test` fehlen. In der Git-Historie liegen alte `solve--playwright-*`-Commits mit einer `playwright.config.ts` im Repo-Root. Das ist ein anderes Setup als mit Nx.

**Ergebnis: Ja, Nx kann das automatisch.** Quelle: Quellcode des Pakets `@nx/playwright@24.0.6` von npm (gelesen, **nicht ausgeführt**). Die Nx-Doku-Seiten lieferten leeren Inhalt, nur die Generator-Optionsseite war lesbar.

**Korrektur (Gregor, 2026-11-24): Der Generator legt kein eigenes E2E-Projekt an.** `@nx/playwright:configuration` schreibt Config und `e2e/` **in den Ordner des Projekts aus `--project`** (im Code: `generateFiles(..., projectConfig.root)`). Mit `--project=bookmonkey-client` landen die Tests also in der App selbst, genau der Konflikt, den Gregor vermutet hat. Nx' eigener Weg (`@nx/angular:application` mit Playwright als E2E-Runner, Code gelesen) ist anders: Er legt ein **separates Projekt `<name>-e2e`** an (`projectType: 'application'`, `targets: {}`, `implicitDependencies: [<app>]`), ruft den Generator mit `directory: 'src'` darauf auf und nimmt für Angular den Port 4200. Diesen Weg übernehmen wir:

```bash
# 1. leeres E2E-Projekt anlegen: apps/bookmonkey-client-e2e/project.json
#    (name, projectType: application, sourceRoot: apps/bookmonkey-client-e2e/src,
#     targets: {}, implicitDependencies: ["bookmonkey-client"])
# 2. Plugin und Konfiguration
npx nx add @nx/playwright
npx nx g @nx/playwright:configuration --project=bookmonkey-client-e2e \
  --directory=src \
  --webServerCommand="npx nx serve bookmonkey-client" \
  --webServerAddress=http://localhost:4200
# 3. ausführen
npx nx e2e bookmonkey-client-e2e
```

Das manuelle `project.json` ist der einzige nicht automatische Schritt (oder Task-Vorgabe im Start-Commit). Ob der Generator damit ohne Fehler läuft, ist aus dem Code geschlossen, **nicht ausgeführt**. Im Worktree prüfen.

(Die Form von `nx add` ist aus der Plugin-Struktur geschlossen, nicht aus der Doku belegt. Alternativ installiert der Generator das Plugin selbst über seinen `init`-Schritt.)

| Thema | Befund aus dem Paketcode |
| ----- | ------------------------ |
| Generator | `@nx/playwright:configuration`, Pflichtoption `--project`. Optionen: `--directory` (Standard `e2e`), `--webServerCommand`, `--webServerAddress`, `--ciWebServerCommand`, `--ciWebServerAddress`, `--js`, `--skipInstall` |
| Erzeugte Dateien | `playwright.config.mts` im Projektordner und `src/example.spec.ts`. Die Config nutzt das Preset `nxE2EPreset`. Außerdem: `tsconfig.json` im Projekt (falls noch keine existiert), Eintrag `ms-playwright.playwright` in `.vscode/extensions.json`, Dev-Dependencies `@nx/playwright`, `@playwright/test`, `@nx/devkit`, `@types/node` |
| Dev-Server | Die Config enthält `webServer: { command, url, reuseExistingServer: true, cwd: workspaceRoot }` und `baseURL` (überschreibbar per `BASE_URL`). Der Server startet also automatisch beim Testlauf |
| Nx-Target | Das Plugin `@nx/playwright/plugin` wird in `nx.json` registriert und leitet die Targets **`e2e`** und **`e2e-ci`** ab. Aufruf: `npx nx e2e bookmonkey-client-e2e` |
| Browser | Der Generator führt `playwright install` selbst aus (außer mit `--skipInstall`) |
| Standardwerte | Ohne Angabe fragt der Generator nach Command und Port, im nicht-interaktiven Modus nimmt er `npx nx serve <projekt>` und `http://localhost:3000` |

**Stolpersteine (aus dem Code abgeleitet, noch nicht gelaufen):**
1. **Port:** Der Standard ist 3000, der Angular-Dev-Server nutzt 4200. `--webServerAddress` muss explizit gesetzt werden. Der `serve`-Target von `bookmonkey-client` hat im `project.json` keinen Port festgelegt.
2. **Beispieltest schlägt fehl:** `src/example.spec.ts` erwartet einen `h1` mit "Welcome". Die Aufgabe muss ihn ersetzen oder löschen.
3. **Kollision mit Vitest:** `apps/bookmonkey-client/vite.config.mts` hat `include: ['**/*.spec.ts']`. Läge `src/example.spec.ts` im App-Ordner, sammelte Vitest ihn im Browser Mode ein, und er scheiterte. **Mit separatem Projekt `bookmonkey-client-e2e` entfällt das:** Das `include` gilt relativ zum Root der App, das Geschwisterprojekt hat keine `vite.config`, und die Root `vitest.config.mts` sammelt nur `vite.config`/`vitest.config`-Dateien ein. Falls die E2E-Dateien trotzdem im App-Ordner liegen sollen, brauchen die Playwright-Specs ein eigenes Suffix (`*.e2e.ts` o. Ä.) und `testMatch` in der Playwright-Config, während Vitest sie per `exclude` auslässt. **Im Worktree prüfen.**
4. **Drei Browser:** Die generierte Config enthält Chromium, Firefox und WebKit, und der Generator installiert alle. Für den Workshop reicht Chromium. Das spart Zeit und Download. Firefox und WebKit auskommentieren oder `playwright install chromium` nutzen, dann `--skipInstall` setzen.
5. **Versionen:** Das Paket verlangt `@playwright/test ^1.36.0`. Im Repo steht `playwright ^1.54.0`, die Versionen müssen zusammenpassen.
6. **Preparation:** Die Browser kommen für den Browser Mode schon über die Preparation. Prüfen, ob dieselben Chromium-Binaries für `@playwright/test` reichen.
