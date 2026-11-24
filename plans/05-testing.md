# Lesson 05: Testing

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Die Teilnehmenden testen das gewachsene Projekt auf mehreren Ebenen. Sie starten mit dem klassischen `TestBed`, erleben dessen Grenzen und lösen sie Schritt für Schritt mit Vitest-Mitteln (`expect.poll`, Browser Mode, Locators mit `expect.element`) ab. Danach durchlaufen sie die Teststufen **Unit, Shallow, Integration** und lernen, wann welche Stufe passt. Zum Schluss sichern sie die Architektur mit Tests ab und prüfen einen Kernpfad per E2E.

**Entscheidung (Gregor, 2026-11-24):** Die Testing Library entfällt als eigener Baustein. Der Browser Mode macht sie weitgehend überflüssig, weil `page.getBy*` und `expect.element` dieselbe Rolle übernehmen (nutzerzentrierte Queries, automatisches Retry). Sie erscheint höchstens als Abgrenzung auf einer Folie.

Beworbene Inhalte: Vitest-Vertiefung, Testabstraktionen, realistische Testpyramide, Component vs. Integration vs. E2E, Mock-Strategien, Testbarkeit als Architekturmerkmal. Leitfrage der ganzen Lesson: **Wann nutze ich welche Teststufe, und wo läuft sie?** Neu dazu: **Architekturtests** (aus Lesson 02 hierher verlegt, Entscheidung E11).

## Voraussetzungen

Lessons 02 bis 04: Nx-Workspace mit Libraries, `ui`-Library und Store. Die Tests laufen mit `nx test`.

Es wird **nicht** vorausgesetzt, dass alle Teilnehmenden schon Tests geschrieben haben. Der erste Task holt genau diese Gruppe ab.

## Dramaturgie

Einfach → schmerzhaft → besser → realistisch → abgesichert:

1. **Abholen:** Alle schreiben einen Test mit dem `TestBed` (10). Das ist die Basis für alle, die noch nie getestet haben, und eine Wiederholung für alle anderen.
2. **Schmerz benennen (Folien):** Die Grenzen der `TestBed`-API werden gezeigt (siehe unten). Das motiviert den Wechsel.
3. **Besser machen:** Derselbe Test wird mit Vitest-Mitteln refaktoriert (20), dann im Browser Mode ausgeführt und die Konfiguration verstanden (30). Danach folgen Locators und `expect.element` (40), die den Komfort auf Nutzerverhalten heben.
4. **Teststufen durchlaufen:** Unit (50), Shallow (60), Integration (70). Jede Stufe hat ihren Task und ihre typische Mock-Strategie.
5. **Einordnen:** Entscheidungshilfe, Testpyramide und Testbarkeit (80).
6. **Absichern:** Architekturtests (90) und ein E2E-Kernpfad (100).

Der rote Faden: **Der Test aus Task 10 wird mehrfach wiederverwendet und besser gemacht.** Die Teilnehmenden sehen denselben Test in drei Reifegraden (TestBed pur, `expect.poll`, Browser-Locators). Ab Task 50 wechselt der Faden von "Werkzeug" zu "Teststufe".

## Tasks

| #   | Task                          | Status                                                  | Min | Lernziel                                                                                        |
| --- | ----------------------------- | ------------------------------------------------------- | --- | ----------------------------------------------------------------------------------------------- |
| 10  | Testbed                       | existiert (`10-testbed`), **Einstieg**                  | 15  | Ersten Komponententest mit `TestBed` schreiben (`createComponent`, `setInput`, `detectChanges`) |
| 20  | Vitest-Refactoring            | neu                                                     | 10  | Test aus Task 10 mit `expect.poll` vereinfachen (läuft ohne Browser)                            |
| 30  | Browser Mode konfigurieren    | neu                                                     | 15  | `vite.config` lesen und ändern (`test.browser`), Test im echten Browser laufen lassen           |
| 40  | Locators und `expect.element` | **ersetzt** `20-testing-library`                        | 15  | Nutzerverhalten statt DOM-Details testen mit `page.getBy*` und `expect.element`                 |
| 50  | Unit-Test                     | existiert (`30-mock-service`), **umgerahmt**, mit Bonus | 20  | Logik ohne Template testen (Service/Store), Abhängigkeiten per Mock ersetzen                    |
| 60  | Shallow-Test                  | existiert (`40-mock-components`), **umgerahmt**         | 15  | Eine Komponente isoliert testen, Kind-Komponenten mocken                                        |
| 70  | Integration-Test              | existiert (`50-http`), **umgerahmt**, mit Bonus         | 15  | Echte Komponenten und Services zusammen, nur die Außengrenze (HTTP) mocken                      |
| 80  | Teststufen einordnen          | neu                                                     | 15  | Entscheidungshilfe anwenden: bisherige Tests einordnen, Testbarkeit als Architekturmerkmal      |
| 90  | Architekturtests              | neu, **aus Lesson 02 verlegt**, **abhängig vom Spike**  | 20  | Architekturregeln als Tests formulieren und in der Pipeline laufen lassen                       |
| 100 | E2E                           | neu                                                     | 20  | Ein Kernpfad mit Playwright                                                                     |

Summe ca. **160 Minuten**. Das ist der größte Block und ein Kürzungskandidat (Reihenfolge: Integration-Bonus, Task 60, Task 30, E2E).

Die Ordnernamen der bestehenden Tasks (`10-testbed` bis `50-http`) ändern sich mit der neuen Nummerierung. Die Solutions im `sample-solution` müssen dafür neu geordnet werden (Rebase).

## Task 10: Testbed als Einstieg

- Der bestehende Task bleibt: `BookCard` mit `TestBed.createComponent`, `setInput`, `detectChanges`, zwei Assertions per `querySelector`.
- Er dient bewusst als Abholung für Teilnehmende ohne Testerfahrung. Nichts voraussetzen, was nicht im Task steht (`describe`, `it`, `beforeEach`, `expect` kurz erklären).
- Der Task muss **absichtlich simpel und "vanilla"** bleiben, weil er in Task 20, 30 und 40 verbessert wird.
- Prüfen: Pfade und Befehle (`nx test <projekt>` statt `npm test`), `BookCard` liegt künftig in einer Library.

## Folien: Grenzen von TestBed (nach Task 10)

Diese Inhalte bekommen **keine Aufgabe**, sie begründen den Wechsel zu Vitest und Testing Library:

| Grenze                                     | Konkret                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Vieles muss explizit gemacht werden        | `configureTestingModule`, Fixture, Inputs per `componentRef.setInput`, Provider, alles von Hand                                                                                                                                                                                                                                                                                                                                        |
| `detectChanges()` muss man selbst aufrufen | Wer es vergisst, sieht kein oder ein veraltetes DOM. Fehlerbild ist schwer zu erkennen.                                                                                                                                                                                                                                                                                                                                                |
| `whenStable()` hat Grenzen                 | Löst auf, wenn die Fixture stabil ist, also keine von Angular verfolgten Async-Tasks mehr laufen. Alles andere (z. B. Fake-Timer, externe Async-Arbeit) ist nicht erfasst. **Genaue Grenzen noch nicht belegt, Video-Quelle abwarten.**                                                                                                                                                                                                |
| Zone vs. Zoneless ändert die Regeln        | Auto-Detect ist laut Angular-Doku bei Zoneless standardmäßig an, bei Zone-basierten Tests aus. Die Zoneless-Doku empfiehlt `await fixture.whenStable()` statt `detectChanges()`. Einmal aktiviertes Auto-Detect lässt sich bei Zoneless nicht mehr abschalten (laut Commit, nicht gegen Release geprüft). Quellen: [Utility APIs](https://angular.dev/guide/testing/utility-apis), [Zoneless](https://v20.angular.dev/guide/zoneless). |
| DOM-Zugriff ist wenig ergonomisch          | `querySelector` und `textContent` koppeln den Test an die Markup-Struktur, nicht an das Nutzerverhalten                                                                                                                                                                                                                                                                                                                                |
| Asynchrone Zustände                        | Warten, Polling und Retry muss man selbst bauen                                                                                                                                                                                                                                                                                                                                                                                        |

Überleitung: **Vitest ist eine Bibliothek, mit der wir das besser machen können.** Der Test aus Task 10 wird deshalb refaktoriert.

## Task 20: Vitest-Refactoring (neu)

Der Test aus Task 10 wird mit Vitest-Mitteln neu geschrieben.

- `expect.poll(() => ...)` ersetzt manuelles Warten und `detectChanges`-Rituale, wo es passt.
- `expect.element(locator)` für Assertions auf Elemente, mit automatischem Retry.
- "Partieller Browser Mode" (Begriff aus dem Entwurf): **Geklärt am 2026-11-24:** Gemeint ist der Mischbetrieb, siehe Abschnitt "Konfiguration" unten. Ältere Recherche (2026-11-20): **spricht dagegen, dass `expect.element` und `page.getBy*` ohne Browser Mode laufen.** Beide gehören zu `vitest/browser` und brauchen die Browser-Runtime. `expect.poll` ist dagegen Vitest-Kern und läuft auch gegen jsdom. Konsequenz: Task 20 nutzt `expect.poll` (jsdom), `expect.element` wandert in Task 30. Die Vitest-Doku kennt keinen partiellen Modus, nur getrennte `projects` (siehe Task 30). Der Browser Mode ist pro Vitest-Projekt (also pro Library) an- oder abschaltbar, nicht pro Testdatei.
- Vorher/Nachher-Vergleich: Wie viel Code fällt weg, was wird lesbarer?

- Konfiguration laut [Vitest `expect`-Config](https://vitest.dev/config/expect#expect-poll): `expect.poll.interval` (Standard 50 ms) und `expect.poll.timeout` (Standard 1000 ms). Dieselben Optionen lassen sich pro Aufruf mit `expect.poll(condition, options)` überschreiben. Die Seite `browser.expect` wurde nicht gelesen.
- `expect.poll` laut [Vitest API](https://vitest.dev/api/expect#poll) (Seite war gekürzt, der Poll-Abschnitt lag im gelesenen Teil): `expect.poll(() => wert, { interval, timeout, message })` wiederholt die Assertion bis sie passt. Der Callback bekommt ein `AbortSignal`. Wirft er, wird bis zum Timeout wiederholt. **Immer `await`**, sonst schlägt der Test seit Vitest 3 mit Warnung fehl. Nicht unterstützt: Snapshot-Matcher, `.resolves`/`.rejects`, `toThrow`. Mit `vi.useFakeTimers` ruft Vitest nach jedem Fehlversuch `vi.advanceTimersByTime(interval)` auf (Anknüpfung für die Folie zu `whenStable` und Timern).
- **Einordnung (Gregor, 2026-11-20):** `expect.poll` steht in der allgemeinen `expect`-API und der allgemeinen Config, nicht in der Browser-Mode-Doku. Es ist also Vitest-Kern und hat nichts mit dem Browser Mode zu tun. Es läuft deshalb auch gegen jsdom. Das ist aus der Doku-Struktur gelesen, nicht ausprobiert (Spike später).
  Offen: Läuft `expect.poll` im Angular-Builder (`@angular/build:unit-test`) wie dokumentiert? Quellen: [Vitest wait-for](https://main.vitest.dev/guide/recipes/wait-for), [Vitest Assertion API](https://vitest.dev/guide/browser/assertion-api).

## Konfiguration: Wie wird der Browser Mode eingeschaltet?

Der Browser Mode ist **nicht immer aktiv**, sondern ein explizites Opt-in. Belegt am `sample-solution` (Stand 2026-11-24, gelesen, nicht gelaufen):

| Stelle                                        | Inhalt                                                                                                                                         |
| --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `libs/<lib>/vite.config.mts` → `test.browser` | `{ enabled: true, provider: playwright(), instances: [{ browser: 'chromium' }] }`. Ohne diesen Block läuft der Test in Node (jsdom/happy-dom). |
| `libs/<lib>/src/test-setup.ts`                | `setupTestBed({ browserMode: true })` aus `@analogjs/vitest-angular/setup-testbed`. Muss zur Config passen.                                    |
| Root `vitest.config.mts` → `test.projects`    | Sammelt alle `vite.config`/`vitest.config` der Projekte. Jedes Projekt entscheidet selbst über `browser.enabled`.                              |
| Nx-Plugin `@nx/vitest` in `nx.json`           | Leitet das Target `test` aus der vite.config ab. Der Nx-Befehl bleibt `nx test <projekt>`.                                                     |
| `apps/rating` (Sonderfall)                    | Nutzt `@angular/build:unit-test` ohne vite.config. Der Browser kommt über die Builder-Option `browsers`.                                       |

Entscheidung (Gregor, 2026-11-24): **Wir lehren Analog + vite.config.** Die Konfiguration ist dort explizit sichtbar, das passt zu Task 30. Der Angular-Builder bekommt höchstens eine Folie zum Unterschied.

### Mischbetrieb pro Testdatei: zwei Projekte in einer Config

Gregor hat am 2026-11-24 eine `vitest.config.mts` geliefert, die **emulierte und Browser-Tests in einer Library** trennt. Das beantwortet die offene Frage nach dem Mischbetrieb pro Testdatei: Die Trennung läuft über den **Dateinamen**.

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

Folgen für den Plan:

- **Namenskonvention als Lehrstoff:** `*.browser.spec.ts` markiert Browser-Tests. Das passt zur Tabelle der Teststufen und zu den Architekturtests (Task 90: Namensregel prüfbar).
- **Task 20 und 30 werden greifbar:** Task 20 (`expect.poll`) liegt in `*.spec.ts` (jsdom), Task 30 legt `*.browser.spec.ts` an und startet damit den Browser Mode.
- **Mögliches StackBlitz-Szenario:** Läuft der Workshop in StackBlitz, braucht es `preview()`. Klären, ob der Workshop dort stattfindet. Sonst entfällt die Weiche.
- **Abweichung vom `sample-solution`:** Dort steht `browser.enabled: true` fest in der vite.config pro Lib (alle Tests im Browser). Die gelieferte Config ist dagegen ein Zweiprojekt-Modell. Entscheiden, welches Modell der Workshop lehrt.
- **Widerspruch zu Task 40:** Die Config lädt `@testing-library/jest-dom/vitest` für jsdom-Tests. Das Paket bleibt also, auch ohne Testing-Library-Queries.
- Die Config verweist auf `./vite.config.mjs` und `apps/demo`, ist also aus einem anderen Projekt. Pfade für den Workshop anpassen.

Offene Punkte:

- Für `rating` (Builder) gilt eine andere Konfiguration. Soll das in der Lesson auftauchen oder bleibt `rating` unerwähnt?
- Mischbetrieb pro Testdatei: **geklärt**, siehe oben (Zweiprojekt-Config mit Dateisuffix). Offen ist nur, welches Modell der Workshop lehrt.
- Provider `playwright()` braucht installierte Browser (Preparation). Fehlerbild ohne Browser prüfen.
- Für Task 30 könnte ein Teil der Aufgabe sein: `browser.enabled` auf `false` setzen, Ergebnis vergleichen (jsdom vs. Browser), dann wieder einschalten.

## Referenz: Browser-Test für Angular (Beispiel von Gregor, 2026-11-24)

Quelle des Beispiels: nicht genannt (Cookbook-Anwendungsfall, vermutlich aus einem Community-Material). Vor Verwendung in Folien klären, ob und wie es zitiert wird. Der Code wird hier nur als Muster für unser Buch-Beispiel gebraucht.

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

**Geprüft:** `nth()` und `first()` existieren. `toHaveLength` auf einem Locator ist **nicht** belegt, Details im Abschnitt "Geprüfte Locator-Details".

### Geprüfte Locator-Details (Vitest-Doku v5.1.1, 2026-11-24)

Quellen: [Locators](https://vitest.dev/api/browser/locators) (vollständig gelesen), [Assertions](https://vitest.dev/api/browser/assertions) (etwa drei Viertel gelesen, Rest nur als Zusammenfassung).

| Frage | Ergebnis |
| ----- | -------- |
| `nth()`, `first()`, `last()` | **Ja.** `nth(index)` ist nullbasiert und wird **wiederholt**, bis das Element da ist. `first()` = `nth(0)`, `last()` = `nth(-1)`. |
| `click()`, `fill()` | **Ja.** `fill(text)` setzt den Wert von `input`, `textarea` oder `contenteditable`. Alle Locator-Methoden sind async, ohne `await` schlägt der Test seit Vitest 3 fehl. |
| `getByTestId()` | **Ja.** Attribut konfigurierbar über `browser.locators.testIdAttribute`. Der Standardwert steht in der gelesenen Doku **nicht**, vermutlich `data-testid` (nicht belegt, im Spike prüfen). Die Doku rät, es erst zu nutzen, wenn andere Locators nicht passen. |
| `toHaveLength` auf einem Locator | **Nicht dokumentiert.** Das Beispiel von Gregor nutzt es, die Doku kennt keinen Zähl-Matcher. Siehe unten. |
| Placeholder im Accessible Name | **Nicht dokumentiert.** Placeholder hat einen eigenen Locator `getByPlaceholder`. Die Doku bevorzugt `getByLabelText`. |
| Mehrfachtreffer | `.element()`, `.query()` und `expect.element` sind **strikt** und werfen bei mehreren Treffern. Für `click`/`fill` ist es nicht ausdrücklich beschrieben, die Doku deutet aber einen Fehler an. Deshalb im Beispiel `.first()`. |
| Matcher in `expect.element` | `toBeDisabled`, `toBeEnabled`, `toBeEmptyDOMElement`, `toBeInTheDocument`, `toBeInvalid`, `toBeRequired`, `toBeValid`, `toBeVisible`, `toBeInViewport`, `toContainElement`, `toContainHTML`, `toHaveAccessibleDescription`, `toHaveAccessibleErrorMessage`, `toHaveAccessibleName`, `toHaveAttribute`, `toHaveClass`, `toHaveFocus`, `toHaveFormValues`, `toHaveStyle`, `toHaveTextContent`, `toMatchTextContent`, `toHaveValue`, `toHaveDisplayValue`, `toBeChecked`, `toBePartiallyChecked`. Zusätzlich `toHaveRole`, `toHaveSelection` und `toMatchScreenshot` (laut Gregor, 2026-11-24, über die Anker [#tohaverole](https://vitest.dev/api/browser/assertions#tohaverole), [#tohaveselection](https://vitest.dev/api/browser/assertions#tohaveselection), [#tomatchscreenshot](https://vitest.dev/api/browser/assertions#tomatchscreenshot) bestätigt, nur `toMatchScreenshot` ist noch experimentell, `toHaveRole` und `toHaveSelection` nicht). |
| Namensabgleich | Die Doku widerspricht sich (exakt per Standard `browser.locators.exact: true` oder Substring ohne Groß-/Kleinschreibung). Im Spike prüfen. |

**Zählen von Elementen** (Vorschlag, die Einzelteile sind belegt, die Kombination wurde nicht ausgeführt):

```ts
// mit Retry, empfohlen
await expect.poll(() => cookbooks.elements().length).toBe(3);

// ohne Retry
expect(cookbooks.elements()).toHaveLength(3);
```

`locator.length` ist ein Getter ohne Retry, `elements()` wirft nie und liefert ein leeres Array. Das Beispiel mit `expect.element(cookbooks).toHaveLength(3)` ist daher **vor der Verwendung in Folien zu prüfen**. In den Folien lieber die belegte Variante mit `expect.poll` zeigen, das knüpft außerdem an Task 20 an.

### Übertragung auf BookMonkey (Entwurf)

Bestehende Elemente in `libs/feature-books` (gelesen):

- `BooksPage` mit `<input type="search" placeholder="Search...">`, einer Liste von `app-book-card` und je Karte einem `Delete`-Button.
- `BookCard` mit `h3` (Titel) und `h4` (Autor).
- Das Suchfeld **filtert und markiert** (Korrektur durch Gregor, 2026-11-24, im Code bestätigt): `BooksPage.booksComputed` filtert die Bücher per `bookMatches(book, markTerm)`, die Direktive `appBookMarker` hebt den Treffer zusätzlich hervor. Ein Test "filtert nach Suchbegriff" lässt sich also direkt vom Cookbook-Beispiel übernehmen.
- `deleteBook` ruft `window.confirm(...)` auf. Vitest mockt `confirm` im Browser Mode (siehe Einschränkungen in Task 30). Für den Delete-Test muss das Verhalten bewusst gesteuert werden (z. B. `vi.spyOn(window, 'confirm')`, im Spike prüfen).

| Cookbook-Beispiel                            | Buch-Variante                                                                                                                                                                                                                                   |
| -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Rezepte nach Autor filtern | Suchbegriff eingeben, nur Karten mit Treffer bleiben sichtbar (Titel wird zusätzlich markiert) |
| `getByRole('textbox', { name: 'Keywords' })` | Das Suchfeld hat nur `placeholder`, kein Label. Die Vitest-Doku sagt nichts dazu, ob der Placeholder in den Namen von `getByRole` einfließt, und rät zu `getByLabelText` statt Placeholder. **Entscheidung (Gregor):** Im Zweifel `getByTestId('search')` (`data-testid` am Input). Alternative: ein `aria-label` ergänzen und `getByRole('searchbox', { name: 'Search' })` nutzen, das wäre der bessere Lehrstoff. |
| `getByRole('heading')` + `toHaveLength(3)` | `getByRole('heading', { level: 3 })` für die Buchtitel. Zählen siehe "Geprüfte Locator-Details" oben. |
| `Add to Cart` + `Cart`                       | `Delete`-Button auf der Karte + `BooksClient`/Store. Der Test prüft, dass das Buch verschwindet. Dafür muss HTTP gemockt werden (Integration, Task 70).                                                                                         |
| `TestBed.inject(Cart)`                       | `TestBed.inject(BookMarkerStore)` für den Marker-Zustand (existiert schon im `BookCard`-Spec)                                                                                                                                                   |

Vorschlag für die **Folie "Minimales Setup"**: Erste Hälfte des Beispiels, auf `BookCard` zugeschnitten: `TestBed.createComponent(BookCard)`, `setInput`, `expect.element(page.getByRole('heading', { level: 3 })).toHaveTextContent('Moby Dick')`. Das hält den roten Faden zu Task 10 (gleiche Komponente, gleiche Assertion).

## Folie: Minimales Setup, Browser Mode plus Angular-Komponente

**Pflichtfolie** (Gregor, 2026-11-24). Sie zeigt sehr einfach, wie Browser Mode und eine Angular-Komponente zusammen aufgebaut werden, und dass es funktioniert. Sie gehört direkt vor Task 30.

Inhalt (Entwurf, aus dem `sample-solution` abgeleitet, **nicht ausgeführt**):

1. **Config:** `vite.config.mts` mit dem Block `test.browser: { enabled: true, provider: playwright(), instances: [{ browser: 'chromium' }] }` und dem `angular()`-Plugin.
2. **Setup:** `test-setup.ts` mit `setupTestBed({ browserMode: true })`.
3. **Test:** Eine Spec mit `TestBed.createComponent`, `page`-Locator und `expect.element(...)`.
4. **Ergebnis:** Ein Befehl (`nx test <projekt>`) und ein Screenshot des grünen Laufs.

Anforderungen an die Folie:

- Drei kurze Codeblöcke (Config, Setup, Test), nicht mehr. Alles andere weglassen.
- Die Stellen, an denen der Browser Mode aktiviert wird, hervorheben (`enabled`, `provider`, `browserMode`).
- Das Beispiel muss die `BookCard` aus Task 10 sein, damit der rote Faden sichtbar bleibt.
- Rendering: Das Beispiel aus dem Abschnitt "Referenz" nutzt nur `TestBed.createComponent`. `vitest-browser-angular` ist damit für die Folie nicht nötig (Vorschlag, im Spike verifizieren, **bevor** die Folie entsteht, damit der gezeigte Code wirklich läuft).

## Task 30: Vitest Browser Mode (neu)

### Warum Browser Mode? (Folienargument, Gregor, 2026-11-24)

- **Problem isolierter Tests:** Sie entsprechen nicht der Realität. Gegen jsdom fehlen Browser-APIs. Tests können grün sein und im echten Browser trotzdem fehlschlagen, weil die Umgebung nicht dieselbe ist.
- **Der Trend:** Tests wandern seit Langem immer mehr in den Browser. Früher waren Browser-Läufe sehr langsam, die Ausführung dauerte lange.
- **Was sich geändert hat:** Browser-Tests werden allgemein immer schneller. Vitest bindet die Browser-APIs als Plattform so geschickt ein, dass man sie ohne große Performance-Bedenken nutzen kann. Gemeint ist die Entwicklung von Browser-Tests insgesamt, **kein Vergleich Browser gegen jsdom**.
- Dieselbe Linie nennt die Vitest-Doku ("Why Browser Mode", siehe Task 30). Die Aussage zur Geschwindigkeit ist Gregors Einschätzung und **nicht gemessen**. Der Spike (Zeit je Lauf im Browser Mode) kann bei Bedarf eine Zahl für die Folie liefern.
- Gegenargument ehrlich nennen: Start von Provider und Browser kostet Zeit, und Browser Mode ersetzt keinen E2E-Runner.

### Quellen Browser Mode

- Locators: <https://vitest.dev/api/browser/locators>
- Assertions: <https://vitest.dev/api/browser/assertions>
- Beide Seiten sind Pflichtlektüre für Task 40. Die Assertions-Seite wurde bereits teilweise gelesen (siehe `expect.element` unten), die Locators-Seite noch nicht. Vor dem Schreiben von Task 40 lesen.

- Derselbe Test läuft im echten Browser (Playwright-Provider).
- Fragen: Was ändert sich bei der Konfiguration? Was ist gleich geblieben? Welche Fehler findet nur der Browser (Layout, echte Events)?
- Konfiguration laut [Vitest-Doku](https://vitest.dev/guide/browser/) (Seite zu v5.1.1 gelesen): `test.browser.enabled: true`, `provider: playwright()` (empfohlen, Paket `@vitest/browser-playwright`), `instances: [{ browser: 'chromium' }]`, `headless`. Ein Provider ist Pflicht. Für Angular gibt es kein offizielles Plugin, die Doku verlinkt `vitest-browser-angular`.
- **Warum Browser Mode (Folienargument)**, laut [Vitest "Why Browser Mode"](https://vitest.dev/guide/browser/why): jsdom und happy-dom simulieren den Browser nur, dadurch sind falsch positive und falsch negative Ergebnisse möglich. Nachteile: längere Initialisierung (Provider und Browser starten) und kein Ersatz für E2E-Runner. Die Doku empfiehlt, Browser Mode um einen eigenen Runner wie Playwright zu ergänzen. Das passt zu Task 100.
- **Gemischter Betrieb:** Die Doku beschreibt keinen partiellen Modus. Am nächsten kommt die Option `projects`: Node-Tests und Browser-Tests laufen als getrennte Projekte, aufgeteilt per Glob. Für den Workshop denkbar: Task 20 im Node-Projekt, Task 30 im Browser-Projekt.
- Bekannte Einschränkungen: `alert`/`confirm` blockieren (Vitest mockt sie), `vi.spyOn` auf Modul-Exports wirft (Workaround `vi.mock(pfad, { spy: true })`).
- `expect.element` laut [Browser-Assertions](https://vitest.dev/api/browser/assertions) (Seite gekürzt, Matcher-Liste am Ende nur zusammengefasst): "Like expect.poll, expect.element retries DOM assertions until they pass or the timeout is reached." Locators werden vor der Assertion per `locator.findElement()` aufgelöst. Empfehlung der Doku: bei `page.getBy*` immer `expect.element` nutzen. Ein einfaches `expect(locator).toX()` wiederholt **nicht**. `timeout` und `interval` übernehmen die Werte aus `expect.poll.timeout` und `expect.poll.interval`. Die Matcher stammen aus `@testing-library/jest-dom`, erweitert um Locators und Retry. Nützlich für die Aufgabe: `toBeVisible`, `toBeEnabled`, `toBeInTheDocument`, `toHaveTextContent`, `toHaveValue`, `toHaveAccessibleName`, `toHaveAttribute`, `toHaveClass`.
- **Offen nur für `expect.element`:** Die Doku formuliert es browser-bezogen (Locators kommen aus `vitest/browser`). Dass es ohne Browser Mode läuft, ist nicht belegt und unwahrscheinlich. `expect.poll` ist davon getrennt (siehe Task 20).
- `expect.element` mit Locators (`page.getBy*`) ist die Kernneuheit hier, mit automatischem Retry.
- Angular-Material dazu: [Ninja Squad: Angular tests with Vitest browser mode](https://blog.ninja-squad.com/2025/11/18/angular-tests-with-vitest-browser-mode), Community-Paket `vitest-browser-angular` (Reife prüfen, Alternative zu `TestBed`-Rendering).
- Brücke: Lesson 03 hat Browser-Tests gegen Stories vorgewärmt. Playwright-Browser sind vorab installiert (Preparation).
- Spike nötig: Browser Mode im Nx-Workspace mit dem Angular-Builder, Zeit je Lauf.

## Task 40: Locators und `expect.element` (ersetzt Testing Library)

- Der bestehende Task `20-testing-library` entfällt in seiner jetzigen Form. Entscheidung siehe Ziel.
- Neuer Inhalt: Der Test aus Task 30 wird auf `page.getBy*` (Role, Label, Text) und `expect.element` umgestellt. Das erzeugt den "nutzerzentrierten" Effekt, den vorher die Testing Library hatte.
- Vergleich für die Folien, dieselbe Prüfung in drei Varianten: `querySelector` (Task 10), `expect.poll` (Task 20), `page.getByRole` + `expect.element` (Task 40).
- Abgrenzung zur Testing Library (nur Folie): Was sie früher leistete (`render`, Queries, `userEvent`), und was davon der Browser Mode übernimmt. Rendering bleibt Aufgabe von `TestBed` oder `vitest-browser-angular` (Reife prüfen).
- Interaktion: `userEvent` und Klicks laufen über Locators (`locator.click()`). Prüfen, ob das für die Aufgabe reicht.
- Hinweis: Die Matcher von `expect.element` stammen aus `@testing-library/jest-dom`. Das Paket bleibt also als Abhängigkeit, aber ohne Testing-Library-Queries.
- Offen: Die `BookCard`-Spec im `sample-solution` nutzt aktuell `@testing-library/angular` (`render`, `screen`). Sie muss für diesen Task umgeschrieben werden (nicht jetzt, erst nach Freigabe des Plans).

## Teststufen: Unit, Shallow, Integration (Tasks 50 bis 70)

Die Teststufen sind **Fragestellungen**, der Browser Mode ist eine **Umgebung**, keine vierte Stufe.

| Stufe       | Was wird getestet                                         | Echt                         | Gemockt                         | Umgebung               | Task |
| ----------- | --------------------------------------------------------- | ---------------------------- | ------------------------------- | ---------------------- | ---- |
| Unit        | Klasse oder Funktion ohne Template (`BooksClient`, Store) | die Einheit selbst           | alle Abhängigkeiten             | Node reicht            | 50   |
| Shallow     | Eine Komponente mit ihrem Template                        | die Komponente               | Kind-Komponenten, ggf. Services | Node oder Browser      | 60   |
| Integration | Mehrere echte Komponenten und Services zusammen           | Komponenten, Services, Store | nur die Außengrenze (HTTP)      | Browser Mode empfohlen | 70   |
| E2E         | Ganze App gegen echtes Backend                            | alles                        | nichts                          | Playwright             | 100  |

Entscheidungshilfe für Task 80 (Entwurf, mit Gregor schärfen):

- Prüfe ich **Logik ohne Rendering**? → Unit.
- Prüfe ich **eine Komponente isoliert**, und ihre Kinder stören? → Shallow.
- Prüfe ich, ob **Teile zusammenspielen**? → Integration.
- Hängt das Ergebnis von **Layout, Fokus, echten Events oder CSS** ab? → Browser Mode als Umgebung.
- Prüfe ich einen **Kernpfad des Nutzers** über das ganze System? → E2E.

Die bestehenden Tasks werden **umgerahmt**, nicht neu geschrieben: `mock-service` → Unit, `mock-components` → Shallow, `http` → Integration. Prüfen, ob die Beispiele die Stufe wirklich treffen (z. B. ob `http` heute eher Unit-Test des Clients ist, siehe `books-client.spec.ts`).

## Architekturtests (verlegt aus Lesson 02)

**Warum hier und nicht in Lesson 02:** Wer noch nie Tests geschrieben hat, wird in Lesson 02 neben ADR, Nx und Boundaries zusätzlich mit einem Test überfordert. Nach den Tasks 10 bis 80 sind alle sicher im Schreiben von Tests, und der Architekturtest ist nur noch ein neuer Anwendungsfall. Außerdem passt Testbarkeit als Architekturmerkmal zum Thema.

Offen: Wo sitzt die Brücke? Lesson 02 verweist auf Lesson 05 ("Grenzen der Werkzeuge", Folien).

### Werkzeug

Genannt wurde "ArcUnit", gemeint ist vermutlich ArchUnit. Für TypeScript kommt **ArchUnitTS** in Frage (npm-Paket `archunit`). Das Paket ist im Workshop-Repo **vorinstalliert** (E6).

Stand der Recherche (README gelesen, **nicht ausprobiert**):

- ArchUnitTS ist **kein offizieller Port** von ArchUnit, sondern eine eigenständige, davon inspirierte Bibliothek.
- Installation: `npm install archunit --save-dev`.
- Funktioniert mit Jest, Vitest, Jasmine und anderen Runnern. Für Vitest nennt die README `globals: true` in der `vitest.config.ts`. Der Matcher heißt `toPassAsync()`.
- Beispiele aus der README: `projectFiles().inFolder('src/presentation/**').shouldNot().dependOnFiles().inFolder('src/database/**')` und `projectFiles().inFolder('src/**').should().haveNoCycles()`. Außerdem Namensregeln und Metriken (`metrics().count().linesOfCode().shouldBeBelow(1000)`).
- **Unklar:** Umgang mit Pfad-Aliasen (`@...`), die Angular und Nx nutzen. Die README sagt dazu nichts im gelesenen Teil. Es gibt ein Angular-Beispiel im Repo (nicht gelesen). Die Nx-Anbindung ist nicht verifiziert.

### Abgrenzung zu Nx Boundaries

Nx Boundaries (Lesson 02) kontrollieren Abhängigkeiten **zwischen Projekten** per Tags. Architekturtests sollen das **ergänzen**, nicht doppeln:

| Ebene                    | Nx Boundaries | Architekturtest |
| ------------------------ | ------------- | --------------- |
| Zwischen Libraries       | ja            | nein (doppelt)  |
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

Vorschlag für den Task: **drei Regeln** (Zyklen, `HttpClient`-Regel, Namenskonvention), eine davon bewusst verletzen und die Fehlermeldung lesen.

### Offene Fragen zu Architekturtests

- Funktioniert ArchUnitTS in einem Nx-Workspace mit Angular-Pfad-Aliasen? **Spike nötig.**
- Wo liegt der Architekturtest? Eigene Library (`architecture-tests`) oder im App-Projekt? Als Nx-Target mit Cache und `affected`?
- Ist ArchUnitTS reif genug, um ihn zu lehren? Alternativen: `dependency-cruiser`, `eslint-plugin-boundaries` (entfällt per E1).
- Wie vermeiden wir, dass der Task Nx Boundaries wiederholt?
- Der Bonus-Generator aus Lesson 02 (Task 90) kann hier wieder aufgegriffen werden: Ein Architekturtest prüft, ob generierte Libraries der Konvention entsprechen.

## Weitere offene Fragen

- **Quelle zu den Grenzen von TestBed:** Gregor hat ein Video als Quelle. Link erfragen. Erste Recherche steht in der Folientabelle oben, `whenStable`-Grenzen fehlen noch.
- **"Partieller Browser Mode":** Geklärt als Mischbetrieb pro Vitest-Projekt, siehe Abschnitt "Konfiguration".
- Zeitbudget: Die Stufen-Struktur lässt die Summe bei ca. 160 Minuten. Reicht das für die neuen Inhalte (Konfiguration, Locators)?
- Testabstraktionen (Harnesses, Page Objects): eigener Task oder Teil von Testing Library?
- Testpyramide: nur Folie oder Aufgabe? Vorschlag: eine kurze Einordnungsaufgabe zu den bisher geschriebenen Tests.
- E2E: Playwright oder Cypress? Playwright wird für Browser-Tests in Lesson 03 ohnehin vorgewärmt.
- Passt der Block in die Zeit oder soll die Hälfte des bestehenden Materials in Bonus?

## Arbeit an den bestehenden Tasks

- Tests laufen künftig unter `nx test`, nicht mehr `ng test`. Alle Befehle und Pfade prüfen.
- Der Task `20-testing-library` wird durch Task 40 (Locators und `expect.element`) ersetzt. `@testing-library/angular` und `@testing-library/user-event` fallen aus `package.json` des Workshop-Repos, `@testing-library/jest-dom` bleibt.
- Die Tasks `30-mock-service`, `40-mock-components`, `50-http` bekommen neue Rahmung als Unit, Shallow, Integration.
- Mock components (Task 60) kann auf die neue `ui`-Library zugreifen. Das macht das Beispiel realistischer.
