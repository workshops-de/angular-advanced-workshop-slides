# Lesson 05: Testing

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf, Entscheidungen weitgehend getroffen**.
> Quellen, Befunde und Referenzcode: [05-testing-recherche.md](./05-testing-recherche.md). Alles dort ist gelesen, nicht ausgeführt.

## Ziel

Die Teilnehmenden testen das gewachsene Projekt auf mehreren Ebenen. Sie starten mit dem klassischen `TestBed`, erleben dessen Grenzen und lösen sie Schritt für Schritt mit Vitest-Mitteln (`expect.poll`, Browser Mode, Locators mit `expect.element`) ab. Danach testen sie einen Service (Unit) und die Navigation (Integration) und lernen, wann welche Teststufe passt. Zum Schluss sichern sie die Architektur mit Tests ab und prüfen einen Kernpfad per E2E mit Playwright.

Beworbene Inhalte: Vitest-Vertiefung, Testabstraktionen, realistische Testpyramide, Component vs. Integration vs. E2E, Mock-Strategien, Testbarkeit als Architekturmerkmal, Architekturtests (aus Lesson 02 hierher verlegt, Entscheidung E11).

Leitfrage: **Wann nutze ich welche Teststufe, und wo läuft sie?**

## Voraussetzungen

Lessons 02 bis 04: Nx-Workspace mit Libraries, `ui`-Library und Store. Die Tests laufen mit `nx test <projekt>`.

Es wird **nicht** vorausgesetzt, dass alle schon Tests geschrieben haben. Task 10 holt genau diese Gruppe ab.

## Entscheidungen (Gregor, 2026-11-24)

| Thema | Entscheidung |
| ----- | ------------ |
| Testing Library | Entfällt als Baustein. Browser Mode (`page.getBy*`, `expect.element`) übernimmt die Rolle. `@testing-library/angular` und `@testing-library/user-event` fallen aus der `package.json`, `@testing-library/jest-dom` bleibt (Matcher für `expect.element`). Höchstens eine Abgrenzungsfolie |
| Browser Mode | **Alles im Browser**, wie im `sample-solution`: `browser.enabled: true` je Lib, Analog plus `vite.config`. Kein Zweiprojekt-Modell (`emulated`/`browser`), kein StackBlitz, der Workshop läuft lokal. Der Browser Mode ist schon eingeschaltet, es gibt keine Konfigurationsaufgabe |
| jsdom | Nur Folie: Es hat geholfen, hat aber Tücken (fehlende Browser-APIs, Tests grün, Browser anders). Browser Mode ist die nächste Umgebung zur Realität |
| Shallow | **Kein eigener Task**, nur Folie. Shallow = Komponente isoliert mit Template, Umgebung frei wählbar. Nicht "Shallow = Mocks" |
| Kind-Komponenten mocken | Nur auf einer Folie erwähnt (`MockComponent`), **keine Aufgabe**, weil man es selten macht |
| Rendering | **`vitest-browser-angular` ist bevorzugt** gegenüber reinem `TestBed.createComponent`. Das Paket ist noch nicht in der `package.json` und muss ergänzt werden. API und Reife beim Schreiben von Task 40 prüfen |
| Suchfeld-Locator | `aria-label` am Suchfeld ergänzen, Test mit `getByRole('searchbox', { name: 'Search' })`. Folie weist auf **UX-Testing** hin (Test wie Nutzende und Hilfstechnik). `getByTestId` nur erwähnt |
| Task 50 | `BooksClient` mit `HttpTestingController`, Fehlertest über **`list()`** |
| Task 10 | Ein **vanilla `TestBed`-Test**, um das Vitest-Refactoring zu motivieren. Die bestehende `BookCard`-Spec nutzt `@testing-library/angular` und muss dafür neu geschrieben werden |
| `rating`-App | Wird in der Lesson weggelassen |
| Testpyramide | Folie, keine Aufgabe |
| E2E-Werkzeug | Playwright |
| Testabstraktionen (Harnesses, Page Objects) | Weggelassen |
| Quelle für `whenStable`-Grenzen | Gibt es nicht. Die Folie zeigt nur, was die Angular-Doku sagt |
| Cookbook-Beispiel | Nur für den Agenten, der die Tests baut, nicht für die Folien |
| Umsetzung | Erst die Tests, danach die Folien. Kein separater Spike, die Tests entstehen direkt im `sample-solution` |

## Dramaturgie

Einfach → schmerzhaft → besser → realistisch → abgesichert:

1. **Abholen:** Alle schreiben einen vanilla `TestBed`-Test (10).
2. **Schmerz benennen (Folien):** Grenzen der `TestBed`-API. Das motiviert den Wechsel.
3. **Besser machen:** Derselbe Test mit `expect.poll` (20), dann den Browser Mode verstehen (30), dann Locators und `expect.element` mit `vitest-browser-angular` (40).
4. **Teststufen:** Unit (50), Integration (70). Shallow, jsdom und `MockComponent` kommen nur als Folie.
5. **Einordnen:** Entscheidungshilfe, Testpyramide, Testbarkeit (80).
6. **Absichern:** Architekturtests (90), Playwright einrichten (100), Kernpfad per E2E (110).

Roter Faden: Der Test aus Task 10 wird dreimal besser (TestBed pur, `expect.poll`, Browser-Locators). Ab Task 50 wechselt der Faden von "Werkzeug" zu "Teststufe".

## Tasks

| #   | Task | Status | Min | Inhalt |
| --- | ---- | ------ | --- | ------ |
| 10  | Testbed | `BookCard`-Spec **neu**, vanilla | 15 | `TestBed.createComponent`, `setInput`, `detectChanges`, zwei Assertions per `querySelector` |
| 20  | Vitest-Refactoring | neu | 10 | Test aus Task 10 mit `expect.poll` vereinfachen. Vorher/Nachher-Vergleich |
| 30  | Browser Mode verstehen | neu, Konfiguration existiert schon | 15 | `vite.config.mts` und `test-setup.ts` lesen, `browser.enabled` aus- und wieder einschalten, jsdom und Browser vergleichen |
| 40  | Suche mit Locators | **ersetzt** `20-testing-library` | 15 | Test für die Suche in `BooksPage`: `vitest-browser-angular`, `page.getByRole('searchbox', { name })`, `fill`, `expect.element`. Danach Testreport öffnen, Coverage ansehen. **Bonus A:** Bereich mit geringer Abdeckung suchen und testen |
| 50  | Unit-Test | `50-http` umgerahmt | 20 | `BooksClient` mit `HttpTestingController`: (a) Bücher laden, (b) Fehler bei Schema-Verstoß über `list()`. `vi.waitFor` im bestehenden Spec auf `expect.poll` umstellen |
| 60  | ~~Shallow~~ | entfällt | 0 | Nur Folie |
| 70  | Integration-Test | neu | 15 | Navigation mit `RouterTestingHarness`: Klick auf Buchkarte führt zur Detailseite, nur HTTP gemockt. **Bonus B:** Delete-Test mit `window.confirm` |
| 80  | Teststufen einordnen | neu | 15 | Bisherige Tests einordnen, Entscheidungshilfe anwenden, Testbarkeit als Architekturmerkmal |
| 90  | Architekturtests | neu, aus Lesson 02 verlegt | 20 | Library `architecture-tests` (`type:test`) im Task anlegen, ArchUnitTS, zwei Tests: keine Zyklen, `feature-books` hängt nicht von `feature-dashboard` ab |
| 100 | Playwright einrichten | neu, **eigener Task** | 15 | E2E-Projekt per Nx-Generator einrichten (Setup existiert noch nicht) |
| 110 | E2E-Kernpfad | neu | 20 | Ein Kernpfad mit Playwright (zum Beispiel Buch suchen, Detailseite öffnen) |

| 120 | Bonus: Time machine | neu, **Bonus**, eigener Task | 10 | Zeitabhängigen Code mit Fake Timers testen: `App` begrüßt nach 6 s per `setTimeout`. `vi.useFakeTimers`, `advanceTimersByTimeAsync`, `expect.element` |

Summe ca. **160 Minuten** (Schätzung für 100 und 110). Kürzungskandidaten in dieser Reihenfolge: Bonus A und B, Task 30, Task 110.

Die Ordnernamen der bestehenden Tasks ändern sich mit der Nummerierung. Die Solutions im `sample-solution` müssen neu geordnet werden (Rebase, `solution-creator`). Die Nummer 60 bleibt frei.

### Task 10: Testbed

- Vanilla Angular, absichtlich simpel, weil Task 20, 30 und 40 ihn verbessern.
- Nichts voraussetzen (`describe`, `it`, `beforeEach`, `expect` kurz erklären). Befehle mit `nx test <projekt>`.
- Die bestehende `BookCard`-Spec nutzt Testing Library und wird durch diese Fassung ersetzt.

### Task 30: Browser Mode verstehen (Hinweis für die Folien)

- Die Solution schreibt den Test bewusst **inline** (`renderBookCard()` mit `page.elementLocator`, `getByRole`, `expect.element`). Eine Abstraktion wäre hier zu früh.
- **Folie, kein Task:** Danach kann gezeigt werden, wie man die Locators in eine kleine Tester-Klasse auslagert (`BookCardTester` mit `fixture`, `root = page.elementLocator(...)`, `title`, `author`). Muster aus [Ninja Squad](https://blog.ninja-squad.com/2025/11/18/angular-tests-with-vitest-browser-mode). Die Testabstraktion bleibt damit Folie und wird nicht Teil der Aufgabe (siehe Entscheidung "Testabstraktionen").

### Task 40: Suche

- `BooksPage` filtert per `bookMatches` und markiert den Treffer per `appBookMarker`. Der Test: Suchbegriff eingeben, nur passende Karten bleiben, Titel per `getByRole('heading', { level: 3 })` prüfen.
- Dafür ergänzt der Task ein `aria-label="Search"` am Suchfeld (Änderung am `sample-solution`).
- Zählen mit `expect.poll(() => locator.elements().length)`, siehe Recherche Abschnitt 4. Ob `toHaveLength` am Locator geht, ist nicht belegt.

### Task 50: Unit

- `books-client.spec.ts` nutzt `HttpTestingController` schon. Es kommen zwei Tests dazu, die Schema-Validierung existiert (`v.parse(BooksCollectionSchema, ...)` in `list()`).

### Task 70: Integration

- `RouterTestingHarness` (Name laut Angular-Doku, Import aus `@angular/router/testing`), echte Routen mit `provideRouter`, den Router nicht mocken. Details in Recherche Abschnitt 7.
- **Nicht belegt:** Klick auf `routerLink`, Zusammenspiel von `routeNativeElement` mit Locators. Beim Schreiben des Tests klären.
- Bonus B: `window.confirm` wird von Vitest im Browser Mode gemockt, das Verhalten muss bewusst gesteuert werden (zum Beispiel `vi.spyOn`).

### Task 120 (Bonus): Time machine

- Eigener Bonus-Task, im Lesson-Ordner `100-time-machine` (die Nummern im Plan und im Ordner weichen ab, siehe Hinweis zur Nummerierung oben).
- Ziel: Zeitabhängigen Code testen, ohne zu warten. Getestet wird `App` (`apps/bookmonkey-client/src/app/app.ts`): Im Konstruktor ändert ein `setTimeout` nach 6000 ms `warmWelcome` von _Angularian_ zu _Hello Angularian_.
- Die Fake-Uhr muss **vor** `render` installiert sein, weil der Timer im Konstruktor startet. `provideRouter([])` ist nötig (Router-Outlet).
- Die Referenz-`app.spec.ts` auf `main` enthält den Timer-Test bewusst **nicht**, sonst wäre der Bonus vorab gelöst.
- Snippet (Musterlösung), Datei `app-greeting.spec.ts`:

```ts
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

  it('greets the attendee after 6 seconds', async () => {
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

- Anknüpfung: `whenStable()` erfasst Fake-Timer nicht (Recherche Abschnitt 1), `expect.poll` ruft mit Fake Timers `advanceTimersByTime` auf.
- **Nicht ausgeführt.** Beim Anlegen der Solution `solution--testing-time-machine` prüfen, ob `expect.element` unter Fake Timers nicht hängt (es pollt selbst mit Timern).

### Task 90: Architekturtests

- Werkzeug: [ArchUnitTS](https://github.com/LukasNiessen/ArchUnitTS) (Paket `archunit`, vorinstalliert).
- Test 1: Dateien sind zyklenfrei (`haveNoCycles`). Test 2: Dateien in `feature-books/` hängen nicht von `feature-dashboard/` ab.
- Test 2 überlappt mit Nx Boundaries (Lesson 02). Das offen sagen. Es ist der Lehrpunkt der Folie: Mit Architekturtests lassen sich die **eigenen Layer** testen, ohne an Boundaries und Vertical Slices gebunden zu sein, also deutlich flexibler.
- Den Test einmal bewusst verletzen, um zu sehen, dass er rot wird (wichtig, falls Pfad-Aliase wie `@...` nicht aufgelöst werden).
- **Ort (Entscheidung Gregor, 2026-11-24):** Die Tests liegen in einer **eigenen Library `architecture-tests` mit dem Tag `type:test`**. Die Library wird **im Task selbst angelegt** (Nx-Generator), nicht vorab. Grund: ArchUnitTS liest Dateien über das Dateisystem, das geht nur in Node, und alle anderen Projekte laufen im Browser Mode. Die neue Library bekommt deshalb eine `vite.config` **ohne** `test.browser` (Node-Umgebung) und läuft als normales `nx test architecture-tests`.
- **Generator (Entscheidung Gregor, 2026-11-24):** `@nx/angular:library` mit dem **Analog-Vitest-Runner** (`unitTestRunner: vitest-analog`). Das ist in `nx.json` schon der Standard für `@nx/angular:library`, also `npx nx g @nx/angular:library architecture-tests --tags=type:test` (Flags vor Ausführung mit `--help` prüfen). Der Generator erzeugt `vite.config.mts` und `test-setup.ts`. **Danach prüfen, dass kein `test.browser` und kein `browserMode: true` gesetzt ist**, sonst läuft der Test im Browser und findet keine Dateien (ArchUnitTS braucht Node). Nicht ausgeführt, Verhalten des Generators aus dem Workspace-Default geschlossen.
- **Cache (gewünscht):** `nx test architecture-tests` soll gecacht werden. Der Test liest aber Quellen **aller** Libraries, die für Nx keine Abhängigkeiten der neuen Library sind. Das Standard-`inputs` des Targets (`default`, `^production`) würde Änderungen in `feature-books` nicht sehen, und der Cache lieferte ein veraltetes grünes Ergebnis. Vorschlag: im `project.json` der Library `targets.test.inputs` um die Quelldateien erweitern, zum Beispiel `{workspaceRoot}/libs/**/*.ts` und `{workspaceRoot}/apps/**/*.ts` (ohne Specs). Das ist der eigentliche Lehrpunkt des Caches im Task. Beim Schreiben prüfen: Änderung in `feature-books` macht den Cache ungültig, Änderung in einer unbeteiligten Datei nicht.
- Bezug zu Lesson 02: Die Tags `type:*` und `scope:*` und die `depConstraints` stehen im `sample-solution` in `eslint.config.mjs`. `type:test` ist neu. Es gibt dort bisher keine Regel für `type:test`, die Library ist damit nicht eingeschränkt. Ob eine eigene Regel nötig ist, beim Anlegen prüfen. Die Library importiert keinen Code der anderen Libs, sie liest Dateien.
- Hinweis zur Überlappung: Die Nx-Regeln mit `scope:books` und `scope:dashboard` verbieten `feature-books` → `feature-dashboard` im `sample-solution` bereits (ESLint). Test 2 prüft dieselbe Regel auf Dateiebene. Das passt zur Überlappungsaussage oben.

### Task 100 und 110: Playwright

Der Workshop läuft schon, und das Playwright-Setup fehlt noch im `sample-solution`. Deshalb ist das Setup ein eigener Task (100).

```bash
# 1. leeres E2E-Projekt: apps/bookmonkey-client-e2e/project.json
#    (projectType: application, sourceRoot: .../src, targets: {},
#     implicitDependencies: ["bookmonkey-client"])
npx nx add @nx/playwright
npx nx g @nx/playwright:configuration --project=bookmonkey-client-e2e \
  --directory=src \
  --webServerCommand="npx nx serve bookmonkey-client" \
  --webServerAddress=http://localhost:4200
npx nx e2e bookmonkey-client-e2e
```

- Der Generator legt **kein** eigenes Projekt an. Er schreibt in den Ordner des Projekts aus `--project`. Das E2E-Projekt als Geschwister der App vermeidet die Kollision mit Vitest (`include: ['**/*.spec.ts']` in der App-Config).
- Stolpersteine: Port 4200 statt Standard 3000, Beispieltest erwartet "Welcome" und schlägt fehl, drei Browser in der Config (Chromium reicht), Versionen von `playwright` und `@playwright/test`. Details in Recherche Abschnitt 10. Alles aus dem Paketcode gelesen, **nicht ausgeführt**.
- Task 110 kann auf die alten `solve--playwright-*`-Commits des `sample-solution` zurückgreifen.

## Teststufen

Die Stufen sind Fragestellungen, der Browser Mode ist die Umgebung für alle (nur Browser Mode, siehe Entscheidungen).

| Stufe | Was wird getestet | Echt | Gemockt | Task |
| ----- | ----------------- | ---- | ------- | ---- |
| Unit | Klasse oder Funktion ohne Template (`BooksClient`) | die Einheit | alle Abhängigkeiten (HTTP) | 50 |
| Shallow | Eine Komponente mit Template, isoliert | die Komponente | optional Kind-Komponenten (`MockComponent`) | nur Folie |
| Integration | Mehrere echte Komponenten, Router und Services | alles außer der Außengrenze | nur HTTP | 70 |
| E2E | Ganze App gegen echtes Backend | alles | nichts | 100, 110 |

Entscheidungshilfe für Task 80 (Entwurf):

- **Logik ohne Rendering?** → Unit.
- **Eine Komponente isoliert, Kinder stören?** → Shallow.
- **Teile spielen zusammen?** → Integration.
- **Kernpfad der Nutzenden über das ganze System?** → E2E.

## Folien (erst nach den Tests)

Die Folien entstehen, wenn die Tests laufen, damit der gezeigte Code wirklich läuft (Werkzeug: `slide-creator`).

| Folie | Inhalt | Position |
| ----- | ------ | -------- |
| Grenzen von TestBed | Tabelle aus Recherche Abschnitt 1, nur Angular-Doku | nach Task 10 |
| Minimales Setup (**Pflicht**) | Drei kurze Codeblöcke (Config, Setup, Test), `BookCard` als Beispiel, `enabled`, `provider`, `browserMode` hervorheben, ein Befehl und grüner Lauf. Mit `vitest-browser-angular`, **vorher im Test verifizieren** | vor Task 30 |
| Warum Browser Mode, jsdom und Shallow | jsdom hat geholfen, hat aber Tücken. Browser Mode ist die nächste Umgebung zur Realität (1:1 wie im Browser). Gegenargument: Start von Provider und Browser kostet Zeit, kein Ersatz für E2E. Shallow-Begriff und `MockComponent` kurz erwähnen | bei Task 30 |
| Locators und UX-Testing | `getByRole` mit `aria-label`, Accessible Name, Testen wie Nutzende. `getByTestId` als Ausnahme. Vergleich `querySelector`, `expect.poll`, `page.getByRole`. Abgrenzung zur Testing Library | bei Task 40 |
| Testpyramide | Folie, keine Aufgabe | bei Task 80 |
| Architekturtests | Flexibler als Nx Boundaries: eigene Layer testbar, nicht an Boundaries und Vertical Slices gebunden | bei Task 90 |

## Umsetzungsreihenfolge

| Schritt | Inhalt | Abhängigkeit |
| ------- | ------ | ------------ |
| 1 | Task 10: vanilla `TestBed`-Spec für `BookCard`, `nx test` | keine |
| 2 | Tasks 20 und 30 | Schritt 1 |
| 3 | Task 40 (inklusive `aria-label` und `vitest-browser-angular` in `package.json`) | Schritt 2 |
| 4 | Task 50 | keine, parallel möglich |
| 5 | Task 70 | Schritt 3 |
| 6 | Task 100 und 110 (Generator im Worktree ausprobieren, dann Task schreiben) | keine |
| 7 | Task 90 (Library `architecture-tests` per Generator anlegen, Aliase klären) | keine |
| 8 | Task 80 | Schritte 2 bis 5 |
| 9 | Solutions neu ordnen (Rebase), danach Folien | alle Tasks |

### Beim Schreiben der Tests zu prüfen

1. `expect.poll` läuft im Browser Mode wie dokumentiert (Task 20).
2. `vitest-browser-angular`: Installation, `render`, Zusammenspiel mit `setupTestBed({ browserMode: true })`.
3. `getByRole('searchbox', { name })` mit `aria-label`, Zählen von Elementen.
4. `RouterTestingHarness` im Browser Mode, Klick auf Buchkarte, URL prüfen.
5. `window.confirm` im Browser Mode steuern (Bonus B).
6. Coverage-Report im Browser Mode: Ausgabeformat, Öffnen.
7. ArchUnitTS: Methodennamen gegen die installierte Version, Pfad-Aliase, Ort des Tests.
8. Playwright-Generator mit manuellem `project.json`, Kollision mit Vitest ausschließen.
9. Startzustand der Lesson: Läuft die Preparation mit Chromium für Vitest und `@playwright/test`?

## Offen

| # | Frage |
| - | ----- |
| 1 | **Architekturtest:** Ort entschieden (Library `architecture-tests`, `type:test`, im Task angelegt). Generator entschieden (`@nx/angular:library`, `vitest-analog`), Cache gewünscht. Offen: Setzt der Generator `test.browser`, und muss es entfernt werden? Reichen die erweiterten `inputs` im `project.json` für einen korrekten Cache (siehe Task 90)? Wie verhält sich `nx affected -t test` (die Library hängt nicht im Projektgraphen an den anderen)? |
| 2 | **Generator ausprobieren:** `@nx/playwright:configuration` im Worktree mit manuellem E2E-`project.json` (nur aus dem Code geschlossen) |
| 3 | **Brücke zu Lesson 02:** Lesson 02 verweist für "Grenzen der Werkzeuge" auf Lesson 05. Wo steht die Folie genau? |
| 4 | **ArchUnitTS reif genug zum Lehren?** Alternative `dependency-cruiser` |
| 5 | **`vitest-browser-angular`:** Reife und API, Aufnahme in die `package.json` |
| 6 | **`MockComponent`:** Auf der Folie nennen. Kommt es aus `ng-mocks` oder aus dem Beispiel `BookCardMock` im `sample-solution`? |
| 7 | **Zeit:** Reichen etwa 160 Minuten? Zeit für Task 100 und 110 ist geschätzt |
| 8 | **Task 80 und Teststufen-Tabelle** mit Gregor schärfen |
