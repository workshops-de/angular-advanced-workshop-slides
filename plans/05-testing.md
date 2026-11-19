# Lesson 05: Testing

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Die Teilnehmenden testen das gewachsene Projekt auf mehreren Ebenen, wählen bewusst Mock-Strategien und sichern die Architektur selbst mit Tests ab.

Beworbene Inhalte: Vitest-Vertiefung, Testabstraktionen, realistische Testpyramide, Component vs. Integration vs. E2E, Mock-Strategien, Testbarkeit als Architekturmerkmal. Neu dazu: **Architekturtests**.

## Voraussetzungen

Lessons 02 bis 04: Nx-Workspace mit Libraries, `ui`-Library und Store. Die Tests laufen mit `nx test`.

## Tasks

| #   | Task                          | Status                        | Min | Lernziel                                                                    |
| --- | ----------------------------- | ----------------------------- | --- | --------------------------------------------------------------------------- |
| 10  | Testbed                       | existiert                     | 15  |                                                                             |
| 20  | Testing Library               | existiert                     | 15  |                                                                             |
| 30  | Mock service                  | existiert (mit Bonus)         | 20  |                                                                             |
| 40  | Mock components               | existiert                     | 15  |                                                                             |
| 50  | Http                          | existiert (mit Bonus)         | 15  |                                                                             |
| 60  | Testpyramide und Testbarkeit  | neu                           | 15  | Bisherige Tests einordnen, Testbarkeit als Architekturmerkmal               |
| 70  | Architekturtests              | neu                           | 20  | Architekturregeln als Tests formulieren und in der Pipeline laufen lassen   |
| 80  | E2E                           | neu                           | 20  | Ein Kernpfad mit Playwright                                                 |

Summe ca. 135 Minuten. Das ist der größte Block und ein Kürzungskandidat.

## Architekturtests (neu)

### Werkzeug

Genannt wurde "ArcUnit", gemeint ist vermutlich ArchUnit. Für TypeScript kommt **ArchUnitTS** in Frage (npm-Paket `archunit`).

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

## Weitere offene Fragen

- Testabstraktionen (Harnesses, Page Objects): eigener Task oder Teil von Testing Library?
- Testpyramide: nur Folie oder Aufgabe? Vorschlag: eine kurze Einordnungsaufgabe zu den bisher geschriebenen Tests.
- E2E: Playwright oder Cypress? Playwright wird für Browser-Tests in Lesson 03 ohnehin vorgewärmt.
- Passt der Block in die Zeit oder soll die Hälfte des bestehenden Materials in Bonus?

## Arbeit an den bestehenden Tasks

- Tests laufen künftig unter `nx test`, nicht mehr `ng test`. Alle Befehle und Pfade prüfen.
- Mock components (Task 40) kann auf die neue `ui`-Library zugreifen. Das macht das Beispiel realistischer.
