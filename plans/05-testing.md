# Lesson 05: Testing

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Die Teilnehmenden testen das gewachsene Projekt auf mehreren Ebenen. Sie starten mit dem klassischen `TestBed`, erleben dessen Grenzen und lösen sie Schritt für Schritt mit Vitest-Mitteln (`expect.poll`, `expect.element`, Browser Mode) und der Testing Library ab. Danach wählen sie bewusst Mock-Strategien und sichern die Architektur selbst mit Tests ab.

Beworbene Inhalte: Vitest-Vertiefung, Testabstraktionen, realistische Testpyramide, Component vs. Integration vs. E2E, Mock-Strategien, Testbarkeit als Architekturmerkmal. Neu dazu: **Architekturtests** (aus Lesson 02 hierher verlegt, Entscheidung E11).

## Voraussetzungen

Lessons 02 bis 04: Nx-Workspace mit Libraries, `ui`-Library und Store. Die Tests laufen mit `nx test`.

Es wird **nicht** vorausgesetzt, dass alle Teilnehmenden schon Tests geschrieben haben. Der erste Task holt genau diese Gruppe ab.

## Dramaturgie

Einfach → schmerzhaft → besser → realistisch → abgesichert:

1. **Abholen:** Alle schreiben einen Test mit dem `TestBed` (10). Das ist die Basis für alle, die noch nie getestet haben, und eine Wiederholung für alle anderen.
2. **Schmerz benennen (Folien):** Die Grenzen der `TestBed`-API werden gezeigt (siehe unten). Das motiviert den Wechsel.
3. **Besser machen:** Derselbe Test wird mit Vitest-Mitteln refaktoriert (20), dann im Browser Mode ausgeführt (30). Danach folgt die Testing Library (40), die den Komfort auf Nutzerverhalten hebt.
4. **Abhängigkeiten kontrollieren:** Mock Service, Mock Components, Http (50 bis 70).
5. **Einordnen:** Testpyramide und Testbarkeit (80).
6. **Absichern:** Architekturtests (90) und ein E2E-Kernpfad (100).

Der rote Faden: **Der Test aus Task 10 wird mehrfach wiederverwendet und besser gemacht.** Die Teilnehmenden sehen denselben Test in drei Reifegraden.

## Tasks

| #   | Task                          | Status                                  | Min | Lernziel                                                                                         |
| --- | ----------------------------- | --------------------------------------- | --- | ------------------------------------------------------------------------------------------------ |
| 10  | Testbed                       | existiert (`10-testbed`), **Einstieg**  | 15  | Ersten Komponententest mit `TestBed` schreiben (`createComponent`, `setInput`, `detectChanges`) |
| 20  | Vitest-Refactoring            | neu                                     | 10  | Test aus Task 10 mit Vitest-Mitteln vereinfachen (`expect.poll`, `expect.element`)               |
| 30  | Vitest Browser Mode           | neu                                     | 15  | Test im echten Browser laufen lassen, Unterschiede zu jsdom/happy-dom erleben                    |
| 40  | Testing Library               | existiert (`20-testing-library`)        | 15  | Nutzerverhalten statt DOM-Details testen                                                         |
| 50  | Mock service                  | existiert (`30-mock-service`, mit Bonus) | 20 |                                                                                                  |
| 60  | Mock components               | existiert (`40-mock-components`)        | 15  |                                                                                                  |
| 70  | Http                          | existiert (`50-http`, mit Bonus)        | 15  |                                                                                                  |
| 80  | Testpyramide und Testbarkeit  | neu                                     | 15  | Bisherige Tests einordnen, Testbarkeit als Architekturmerkmal                                    |
| 90  | Architekturtests              | neu, **aus Lesson 02 verlegt**, **abhängig vom Spike** | 20 | Architekturregeln als Tests formulieren und in der Pipeline laufen lassen          |
| 100 | E2E                           | neu                                     | 20  | Ein Kernpfad mit Playwright                                                                      |

Summe ca. **160 Minuten**. Das ist der größte Block und ein Kürzungskandidat (Reihenfolge: Http-Bonus, Mock components, Task 30, E2E).

Die Ordnernamen der bestehenden Tasks (`10-testbed` bis `50-http`) ändern sich mit der neuen Nummerierung. Die Solutions im `sample-solution` müssen dafür neu geordnet werden (Rebase).

## Task 10: Testbed als Einstieg

- Der bestehende Task bleibt: `BookCard` mit `TestBed.createComponent`, `setInput`, `detectChanges`, zwei Assertions per `querySelector`.
- Er dient bewusst als Abholung für Teilnehmende ohne Testerfahrung. Nichts voraussetzen, was nicht im Task steht (`describe`, `it`, `beforeEach`, `expect` kurz erklären).
- Der Task muss **absichtlich simpel und "vanilla"** bleiben, weil er in Task 20, 30 und 40 verbessert wird.
- Prüfen: Pfade und Befehle (`nx test <projekt>` statt `npm test`), `BookCard` liegt künftig in einer Library.

## Folien: Grenzen von TestBed (nach Task 10)

Diese Inhalte bekommen **keine Aufgabe**, sie begründen den Wechsel zu Vitest und Testing Library:

| Grenze                                           | Konkret                                                                                                   |
| ------------------------------------------------ | --------------------------------------------------------------------------------------------------------- |
| Vieles muss explizit gemacht werden              | `configureTestingModule`, Fixture, Inputs per `componentRef.setInput`, Provider, alles von Hand           |
| `detectChanges()` muss man selbst aufrufen       | Wer es vergisst, sieht kein oder ein veraltetes DOM. Fehlerbild ist schwer zu erkennen.                    |
| `whenStable()` hat Grenzen                       | Löst auf, wenn die Fixture stabil ist, also keine von Angular verfolgten Async-Tasks mehr laufen. Alles andere (z. B. Fake-Timer, externe Async-Arbeit) ist nicht erfasst. **Genaue Grenzen noch nicht belegt, Video-Quelle abwarten.** |
| Zone vs. Zoneless ändert die Regeln              | Auto-Detect ist laut Angular-Doku bei Zoneless standardmäßig an, bei Zone-basierten Tests aus. Die Zoneless-Doku empfiehlt `await fixture.whenStable()` statt `detectChanges()`. Einmal aktiviertes Auto-Detect lässt sich bei Zoneless nicht mehr abschalten (laut Commit, nicht gegen Release geprüft). Quellen: [Utility APIs](https://angular.dev/guide/testing/utility-apis), [Zoneless](https://v20.angular.dev/guide/zoneless). |
| DOM-Zugriff ist wenig ergonomisch                | `querySelector` und `textContent` koppeln den Test an die Markup-Struktur, nicht an das Nutzerverhalten    |
| Asynchrone Zustände                              | Warten, Polling und Retry muss man selbst bauen                                                           |

Überleitung: **Vitest ist eine Bibliothek, mit der wir das besser machen können.** Der Test aus Task 10 wird deshalb refaktoriert.

## Task 20: Vitest-Refactoring (neu)

Der Test aus Task 10 wird mit Vitest-Mitteln neu geschrieben.

- `expect.poll(() => ...)` ersetzt manuelles Warten und `detectChanges`-Rituale, wo es passt.
- `expect.element(locator)` für Assertions auf Elemente, mit automatischem Retry.
- "Partieller Browser Mode" (Begriff aus dem Entwurf): **Recherche (2026-11-20) spricht dagegen, dass `expect.element` und `page.getBy*` ohne Browser Mode laufen.** Beide gehören zu `vitest/browser` und brauchen die Browser-Runtime. `expect.poll` ist dagegen Vitest-Kern und läuft auch gegen jsdom. Konsequenz: Task 20 nutzt `expect.poll` (jsdom), `expect.element` wandert in Task 30. Die Vitest-Doku kennt keinen partiellen Modus, nur getrennte `projects` (siehe Task 30). Mit Gregor klären, was "partiell" meint.
- Vorher/Nachher-Vergleich: Wie viel Code fällt weg, was wird lesbarer?

- Konfiguration laut [Vitest `expect`-Config](https://vitest.dev/config/expect#expect-poll): `expect.poll.interval` (Standard 50 ms) und `expect.poll.timeout` (Standard 1000 ms). Dieselben Optionen lassen sich pro Aufruf mit `expect.poll(condition, options)` überschreiben. Die Seite `browser.expect` wurde nicht gelesen.
- `expect.poll` laut [Vitest API](https://vitest.dev/api/expect#poll) (Seite war gekürzt, der Poll-Abschnitt lag im gelesenen Teil): `expect.poll(() => wert, { interval, timeout, message })` wiederholt die Assertion bis sie passt. Der Callback bekommt ein `AbortSignal`. Wirft er, wird bis zum Timeout wiederholt. **Immer `await`**, sonst schlägt der Test seit Vitest 3 mit Warnung fehl. Nicht unterstützt: Snapshot-Matcher, `.resolves`/`.rejects`, `toThrow`. Mit `vi.useFakeTimers` ruft Vitest nach jedem Fehlversuch `vi.advanceTimersByTime(interval)` auf (Anknüpfung für die Folie zu `whenStable` und Timern).
- **Einordnung (Gregor, 2026-11-20):** `expect.poll` steht in der allgemeinen `expect`-API und der allgemeinen Config, nicht in der Browser-Mode-Doku. Es ist also Vitest-Kern und hat nichts mit dem Browser Mode zu tun. Es läuft deshalb auch gegen jsdom. Das ist aus der Doku-Struktur gelesen, nicht ausprobiert (Spike später).
Offen: Läuft `expect.poll` im Angular-Builder (`@angular/build:unit-test`) wie dokumentiert? Quellen: [Vitest wait-for](https://main.vitest.dev/guide/recipes/wait-for), [Vitest Assertion API](https://vitest.dev/guide/browser/assertion-api).

## Task 30: Vitest Browser Mode (neu)

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

## Task 40: Testing Library

- Bestehender Task. Nach Task 20 und 30 kommt die Testing Library bewusst **danach**, wenn die Vitest-Grundlagen sitzen.
- Neuer Bezug: Vergleich desselben Tests in drei Varianten (TestBed pur, Vitest-Mittel, Testing Library). Welche liest sich am besten und warum?
- Abgrenzung für die Folien: Locators aus dem Browser Mode (`getByRole`) und Testing-Library-Queries überlappen. Wann welche?

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

| Ebene                      | Nx Boundaries | Architekturtest |
| -------------------------- | ------------- | --------------- |
| Zwischen Libraries         | ja            | nein (doppelt)  |
| Innerhalb einer Library    | nein          | ja              |
| Namens- und Ordnerregeln   | nein          | ja              |
| Zyklen                     | teilweise     | ja              |
| Umfang/Metriken            | nein          | ja              |

### Kandidaten für günstige, gute Tests

Kriterium: wenig Aufwand, aussagekräftig, kaum Fehlalarme.

| Regel                                                                                   | Warum gut                                                  | Aufwand |
| --------------------------------------------------------------------------------------- | ---------------------------------------------------------- | ------- |
| Keine Zyklen innerhalb von Features (`haveNoCycles`)                                    | Zyklen sind ein klassischer Anti-Pattern-Fund              | niedrig |
| `HttpClient` nur in `data-access`, nie in Komponenten                                   | schützt die Schichtentrennung, sofort verständlich          | niedrig |
| Features importieren sich innerhalb einer Library nicht gegenseitig                     | hält Feature-Slices sauber                                 | niedrig |
| Namenskonvention (`*.store.ts` in `data-access`, `*-api-client.ts`)                     | zeigt Namens-Regeln, günstig                               | niedrig |
| Dateigröße oder Zeilenzahl unter Grenzwert                                              | zeigt Metriken, allerdings oft Fehlalarme bei Grenzwerten  | niedrig |

Vorschlag für den Task: **drei Regeln** (Zyklen, `HttpClient`-Regel, Namenskonvention), eine davon bewusst verletzen und die Fehlermeldung lesen.

### Offene Fragen zu Architekturtests

- Funktioniert ArchUnitTS in einem Nx-Workspace mit Angular-Pfad-Aliasen? **Spike nötig.**
- Wo liegt der Architekturtest? Eigene Library (`architecture-tests`) oder im App-Projekt? Als Nx-Target mit Cache und `affected`?
- Ist ArchUnitTS reif genug, um ihn zu lehren? Alternativen: `dependency-cruiser`, `eslint-plugin-boundaries` (entfällt per E1).
- Wie vermeiden wir, dass der Task Nx Boundaries wiederholt?
- Der Bonus-Generator aus Lesson 02 (Task 90) kann hier wieder aufgegriffen werden: Ein Architekturtest prüft, ob generierte Libraries der Konvention entsprechen.

## Weitere offene Fragen

- **Quelle zu den Grenzen von TestBed:** Gregor hat ein Video als Quelle. Link erfragen. Erste Recherche steht in der Folientabelle oben, `whenStable`-Grenzen fehlen noch.
- **"Partieller Browser Mode":** Recherche legt nahe, dass das ohne Browser nicht geht (siehe Task 20). Mit Gregor klären.
- Testabstraktionen (Harnesses, Page Objects): eigener Task oder Teil von Testing Library?
- Testpyramide: nur Folie oder Aufgabe? Vorschlag: eine kurze Einordnungsaufgabe zu den bisher geschriebenen Tests.
- E2E: Playwright oder Cypress? Playwright wird für Browser-Tests in Lesson 03 ohnehin vorgewärmt.
- Passt der Block in die Zeit oder soll die Hälfte des bestehenden Materials in Bonus?

## Arbeit an den bestehenden Tasks

- Tests laufen künftig unter `nx test`, nicht mehr `ng test`. Alle Befehle und Pfade prüfen.
- Testing Library rückt von Position 2 auf Position 4 (Task 40), die Aufgabenbeschreibung muss auf den vorherigen Vitest-Test Bezug nehmen.
- Mock components (Task 60) kann auf die neue `ui`-Library zugreifen. Das macht das Beispiel realistischer.
