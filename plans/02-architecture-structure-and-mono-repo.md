# Lesson 02: Architecture: Structure & Mono-Repo (Nx)

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Die Bookmonkey-App bekommt eine feature-basierte Struktur und wird mit Nx zum Mono-Repo. Die Teilnehmenden zerlegen sie in Libraries und lassen Nx die Abhängigkeiten zwischen diesen Libraries erzwingen.

Beworbene Inhalte, die hier landen: Feature-/Domain-basiert, Struktur für Enterprise-Plattformen, Multi- vs. Monorepo, Tool-Überblick, Architektur (Teil von Mono-Repo-Tools), Codeanalyse (Architekturtest).

## Vorinstallierte Pakete (Entscheidung E6)

Diese Pakete sind im Workshop-Repo bereits in `package.json` und werden **nicht** im Task installiert:

| Paket        | Wofür                                              |
| ------------ | -------------------------------------------------- |
| `@nx/angular` (Nx-Angular-Plugin) | Nx-Migration (Task 20), Library-Generatoren (Task 30) |
| `archunit` (ArchUnitTS) | Architekturtest (Task 60)                          |
| `valibot`    | Schema im Bonus-Generator (Task 70)                |

Folgen:

- Die Tasks beginnen direkt mit dem Anwenden (`nx init`, Generator, Test schreiben). Kein `npm install` in der Aufgabenzeit.
- Das Aufwärmen gehört in die Preparation (Lesson 01), siehe Master-Plan Abschnitt 7.
- Der Spike muss prüfen, ob `nx init` mit vorinstalliertem `@nx/angular` sauber läuft (Version, bereits vorhandene Einträge in `package.json`, keine doppelte Installation).
- Die Boundaries-Regel braucht zusätzlich `@nx/eslint-plugin` und `@nx/devkit`. Prüfen, ob das ebenfalls vorinstalliert wird.
- Valibot ist damit schon vor Lesson 04 verfügbar. Ob Lesson 04 es nutzt, entscheidet der Detailplan 04.

## Voraussetzungen

Lesson 01 (laufende App). Ausgangspunkt ist `main` des Workshop-Repos.

## Dramaturgie

Konvention → sichtbar → physisch → erzwungen → genutzt → abgesichert:

1. Die Struktur wird als Ordnerkonvention eingeführt (Task 10).
2. Nx macht sie im Graph sichtbar (20).
3. Die Libraries machen sie physisch. Am Ende von Task 30 bauen die Teilnehmenden **bewusst einen Verstoß** ein (z. B. `ui` importiert aus `data-access`). Nichts schlägt an, nur `nx graph` zeigt die falsche Kante.
4. Die Boundaries sind die Antwort auf dieses selbst erzeugte Problem (40).
5. Affected und eigene Commands nutzen die Struktur (50).
6. Ein Architekturtest sichert ab, was Nx nicht ausdrücken kann (60, abhängig vom Spike).
7. Bonus: Ein Generator automatisiert die Struktur (70).

## Tasks

| #   | Task                           | Status                             | Min | Lernziel                                                                                      |
| --- | ------------------------------ | ---------------------------------- | --- | --------------------------------------------------------------------------------------------- |
| 10  | Project Structure              | existiert (`02-project-structure`) | 10  | Feature-Slices mit `BookShell`, `features/*`, `data/`                                         |
| 20  | Nx Migration                   | neu                                | 15  | `nx init`, Ergebnis lesen (`nx.json`, `project.json`), `nx graph`, erster Cache-Treffer als Appetizer |
| 30  | Libraries extrahieren          | neu                                | 15  | Feature-Ordner und `data/` werden Libraries (`feature-*`, `data-access`, `ui`). Abschluss: bewusster Verstoß. |
| 40  | Enforce Module Boundaries      | existiert, **umbauen**             | 15  | Tags und `depConstraints` mit `@nx/enforce-module-boundaries`. Stufe 1: nur `type`. Stufe 2 (oder Bonus): `scope`. |
| 50  | Affected und eigene Commands   | neu                                | 15  | Cache und `nx affected` (inkl. `--graph`, `--explain`), eigenes Target mit `nx:run-commands`, `dependsOn`, `nx run-many`. Brücke zu CI (Lesson 08). |
| 60  | Architekturtest (ArchUnitTS)   | neu, **abhängig vom Spike**        | 15  | Eine Regel, die Nx nicht kann (Zyklen, Namenskonvention oder "keine `HttpClient`-Injection in Komponenten"). Abgrenzung: Nx prüft Projekt gegen Projekt, ArchUnit Dateien und Ordner. |
| 70  | Bonus: Generator für ein Aggregat | neu, **Bonus**                  | 20+ | Eigener Nx-Generator, der eine Library mit Grundstruktur für ein Aggregat erzeugt (siehe unten). |

Summe ohne Bonus: 70 min. Das Thema Mono vs. Multi Repo ist reiner Folieninhalt (siehe nächster Abschnitt) und zählt hier nicht als Task.

### Task 50: Affected und eigene Commands

Idee: Zuerst eine Änderung in `ui` machen und mit `nx affected -t test --graph` sehen, welche Projekte betroffen sind. Danach ein eigenes Target anlegen, das die Struktur nutzt.

- Cache: zweiter Lauf von `build` oder `test` kommt aus dem Cache (`targetDefaults` mit `"cache": true`).
- `nx affected -t lint test build`: Wirkung einer Änderung in `ui` gegenüber einer Änderung in einer Feature-Library.
- `--explain` zeigt, warum ein Projekt betroffen ist.
- Eigener Command als Target in `project.json` oder `nx.json`, z. B. `nx:run-commands` für einen Check oder ein Skript. Kandidat: ein Target `check`, das über `dependsOn` Lint und Test bündelt.
- `nx run-many -t check` über alle Projekte, danach dasselbe nur affected.
- Offen: Konkretes Beispiel für den eigenen Command festlegen. Er sollte später in der CI (Lesson 08) und im Architekturtest (Task 60) wiederverwendbar sein.

### Task 70 (Bonus): Generator für ein Aggregat

Motto: Eine Library mit der Grundstruktur für ein Aggregat bootstrappen. Für sehr ambitionierte Teilnehmende.

Der Generator erzeugt (Ausgangsidee, am Beispiel `book`):

| Baustein  | Inhalt                                                                          |
| --------- | ------------------------------------------------------------------------------- |
| routing   | `<aggregate>.routes.ts` mit lazy Route auf die Page                             |
| page      | Einstiegskomponente (Standalone, Signals)                                       |
| client    | HTTP-Client für das Aggregat                                                    |
| model     | Typ, aus dem Schema abgeleitet                                                  |
| schema    | Valibot-Schema, Typ per `v.InferOutput`, Parsing der Client-Antwort             |
| Metadaten | `project.json` mit korrekten Tags, `index.ts` als öffentliche API               |

Vorgehen: `nx g @nx/plugin:plugin` bzw. `@nx/plugin:generator` (zu recherchieren), Template-Dateien mit `__name__`-Platzhaltern, Schema-Optionen für `name`, `scope`. Ergebnis wird mit `nx graph` und den Boundaries aus Task 40 geprüft.

- Verknüpfung: Der Generator darf die Boundaries nicht verletzen. Sonst scheitert Task 40 am eigenen Output.
- Verknüpfung: Ein ArchUnit-Test (Task 60) kann prüfen, ob generierte Libraries der Konvention entsprechen.
- Bonus-Dateien: `bonus.md` zum Task 50 oder eigener Task-Ordner. Entscheidung bei der Umsetzung.

## Folieninhalte ohne Task

Diese Themen sind beworbene Inhalte, bekommen aber **keine Aufgabe**. Sie leben ausschließlich in den Slides (Google Slides, siehe CLAUDE.md). Die Position gibt an, wo sie im Ablauf der Tasks stehen.

| Thema                                  | Position im Ablauf        | Inhalt (Entwurf)                                                                                                           | Beworben als                           |
| -------------------------------------- | ------------------------- | -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------- |
| Feature-/Domain-basierte Struktur      | vor Task 10               | Warum nach Feature statt nach Schicht schneiden. Domänen und Aggregate, Vertical Slices, Struktur für Enterprise-Plattformen. | Feature-/Domain-basiert, Enterprise-Struktur |
| Mono vs. Multi Repo                    | nach Task 10, vor Task 20 | Warum-Frage und Entscheidungskriterien (Team- und Codebasis-Größe, Release-Kopplung, Code-Sharing, CI-Kosten, Ownership). Diskussion statt Aufgabe. | Multi- vs. Monorepo                    |
| Tool-Überblick: Nx, Bit, Rush, Turborepo | nach Task 20            | Vergleich anhand dessen, was die Teilnehmenden gerade mit Nx erlebt haben (Graph, Cache, Generatoren).                     | Tool-Überblick                         |
| Library-Typen und Tag-Modell           | vor Task 40               | Konzept `type` (feature → data-access → ui) und `scope`, erlaubte Abhängigkeitsrichtung. Vorbereitung der `depConstraints`. | Architektur (Teil von Mono-Repo-Tools) |
| Grenzen der Werkzeuge                  | vor Task 60               | Was Nx Boundaries prüft (Projekt gegen Projekt) und was nicht (Dateien, Zyklen, Namen). Überleitung zum Architekturtest.   | Architektur, Codeanalyse               |
| Brücke zu CI und Governance            | nach Task 50              | Ausblick auf Lesson 08: `nx affected` und Architekturtests in der Pipeline.                                                 | CI, CD                                 |

Offen: Welche dieser Themen bekommen eine eigene Folie, welche nur eine Sprechernotiz? Die Folien selbst liegen in Google Slides und werden über den Skill `slide-creator` abgesichert.

## Umbau des bestehenden Boundaries-Tasks

Heute: `eslint-plugin-boundaries` mit Elementtypen nach Ordner (`book`, `common-components`, `shell`).
Künftig (Entscheidung E1): ausschließlich Nx.

| Heute                                         | Künftig                                                           |
| --------------------------------------------- | ----------------------------------------------------------------- |
| `eslint-import-resolver-typescript` installieren | entfällt (Nx löst Projektgrenzen selbst auf)                     |
| `boundaries/elements` nach Dateimuster        | Tags in `project.json`, z. B. `scope:book`, `type:feature`, `type:data-access`, `type:ui` |
| `boundaries/element-types` Regeln             | `depConstraints` in `eslint.config.mjs`                           |
| `@common-components` Alias                    | wird zum Import einer `ui`-Library                                |

Vorschlag für das Tag-Modell (zu validieren): zwei Dimensionen, `scope` (welche Domäne) und `type` (feature → data-access → ui, nur abwärts).

## Wichtig für die Reihenfolge

- Project Structure muss **vor** der Nx-Migration stehen, weil die Feature-Ordner später 1:1 zu Libraries werden.
- Die in diesem Block angelegten Libraries und Tags sind die Grundlage für alle späteren Lessons (UI-Library in 03, `data-access` in 04, Affected in 08).

## Offene Fragen

- **Tag der Aggregat-Library:** Sie bündelt Page, Client und Model in einer Library, was gegen die Trennung `feature` / `data-access` / `ui` läuft. Entweder ein Tag `type:feature` mit interner Struktur oder der Generator erzeugt mehrere Libraries.
- **Eigener Command in Task 50:** Welches konkrete Target? Siehe Task 50.
- **ArchUnitTS in 02 statt 05:** Installation ist durch E6 erledigt, offen bleibt der Spike (Pfad-Aliase, Nx-Anbindung). Bei Umzug ändern sich E5, Plan 05 und die Lesson-Tabelle im Master-Plan.
- **`nx init` standalone oder `--integrated`?** Standalone behält das Dateilayout, integrated verschiebt nach `apps/` und `libs/`. Entscheidung im Spike.
- **Welche Libraries genau?** Nur drei Typen (feature, data-access, ui) oder feiner?
- **Library-Generatoren:** Befehle für Angular-Libraries in Nx sind noch nicht recherchiert.
- Tool-Überblick: entschieden, reiner Folieninhalt (siehe "Folieninhalte ohne Task").

## Risiken

- Die Tasks summieren sich auf 70 min plus Bonus, dazu kommt die Folienzeit. Bei Zeitnot zuerst `scope`-Tags und Task 60 kürzen.
- Vorinstallierte Pakete können mit `nx init` kollidieren (Versionen, Dopplungen). Im Spike prüfen.
- Die Nx-Migration kann je nach Angular-Version Stolpersteine haben. Ohne Spike ist die Zeitschätzung unsicher.
- Imports und Pfad-Aliase beim Verschieben sind fehleranfällig. Checkpoint-Tags nach Task 20 und 30 sind sinnvoll.
