# Lesson 02: Architecture: Structure & Mono-Repo (Nx)

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Das Team trifft zuerst **Architekturentscheidungen und hält sie als ADR fest**. Danach setzt es sie um: Die Bookmonkey-App bekommt eine feature-basierte Struktur und wird mit Nx zum Mono-Repo. Die Teilnehmenden zerlegen sie in Libraries und lassen Nx und einen Architekturtest die Entscheidungen absichern.

Beworbene Inhalte, die hier landen: Architektur-Trade-off-Analyse, Architekturarbeit im Team, Feature-/Domain-basiert, Struktur für Enterprise-Plattformen, Multi- vs. Monorepo, Tool-Überblick, Architektur (Teil von Mono-Repo-Tools), Codeanalyse (Architekturtest).

Diese Lesson übernimmt die frühere Lesson 06 (Runtime Configuration und ADR). Siehe [Detailplan 06](./06-architecture-configuration-and-decisions.md).

## Vorinstallierte Pakete (Entscheidung E6)

Diese Pakete sind im Workshop-Repo bereits in `package.json` und werden **nicht** im Task installiert:

| Paket        | Wofür                                              |
| ------------ | -------------------------------------------------- |
| `@nx/angular` (Nx-Angular-Plugin) | Nx-Migration (Task 30), Library-Generatoren (Task 40) |
| `archunit` (ArchUnitTS) | Architekturtest (Task 70)                          |
| `valibot`    | Schema im Bonus-Generator (Task 90)                |

Folgen:

- Die Tasks beginnen direkt mit dem Anwenden (`nx init`, Generator, Test schreiben). Kein `npm install` in der Aufgabenzeit.
- Das Aufwärmen gehört in die Preparation (Lesson 01), siehe Master-Plan Abschnitt 7.
- Der Spike muss prüfen, ob `nx init` mit vorinstalliertem `@nx/angular` sauber läuft (Version, bereits vorhandene Einträge in `package.json`, keine doppelte Installation).
- Die Boundaries-Regel braucht zusätzlich `@nx/eslint-plugin` und `@nx/devkit`. Prüfen, ob das ebenfalls vorinstalliert wird.
- Valibot ist damit schon vor Lesson 04 verfügbar. Ob Lesson 04 es nutzt, entscheidet der Detailplan 04.

## Voraussetzungen

Lesson 01 (laufende App). Ausgangspunkt ist `main` des Workshop-Repos.

## Dramaturgie

Entscheiden → Konvention → sichtbar → physisch → erzwungen → genutzt → abgesichert:

1. **Entscheiden:** Das Team legt Architekturentscheidungen fest und hält sie als ADR fest (Task 10). Das steht am Anfang, weil alles Folgende diese Entscheidungen umsetzt und absichert.
2. Die Struktur wird als Ordnerkonvention eingeführt (20).
3. Nx macht sie im Graph sichtbar (30).
4. Die Libraries machen sie physisch. Am Ende von Task 40 bauen die Teilnehmenden **bewusst einen Verstoß** ein (z. B. `ui` importiert aus `data-access`). Nichts schlägt an, nur `nx graph` zeigt die falsche Kante.
5. Die Boundaries sind die Antwort auf dieses selbst erzeugte Problem (50). Die Tag-Regeln werden im ADR als Konsequenz nachgetragen.
6. Affected und eigene Commands nutzen die Struktur (60).
7. Ein Architekturtest sichert ab, was Nx nicht ausdrücken kann (70, abhängig vom Spike).
8. Bonus: Runtime Configuration (80) und ein Generator, der die Struktur automatisiert (90).

Der rote Faden: **Entscheidung → Umsetzung → Absicherung.** Der ADR aus Task 10 ist die Quelle, Boundaries (50) und Architekturtest (70) sind die Absicherung im Code.

## Tasks

| #   | Task                           | Status                             | Min | Lernziel                                                                                      |
| --- | ------------------------------ | ---------------------------------- | --- | --------------------------------------------------------------------------------------------- |
| 10  | Architecture Decision Record   | existiert (`05-architecture-decision-record`), **umbauen** | 10  | Entscheidungen im Team festhalten (Kontext, Optionen, Entscheidung, Konsequenzen). Inhalt: Feature-basierte Struktur und Mono-Repo mit Nx. |
| 20  | Project Structure              | existiert (`02-project-structure`) | 10  | Feature-Slices mit `BookShell`, `features/*`, `data/`                                         |
| 30  | Nx Migration                   | neu                                | 15  | `nx init`, Ergebnis lesen (`nx.json`, `project.json`), `nx graph`, erster Cache-Treffer als Appetizer |
| 40  | Libraries extrahieren          | neu                                | 15  | Feature-Ordner und `data/` werden Libraries (`feature-*`, `data-access`, `ui`). Abschluss: bewusster Verstoß. |
| 50  | Enforce Module Boundaries      | existiert, **umbauen**             | 15  | Tags und `depConstraints` mit `@nx/enforce-module-boundaries`. Stufe 1: nur `type`. Stufe 2 (oder Bonus): `scope`. ADR um die Regeln ergänzen. |
| 60  | Affected und eigene Commands   | neu                                | 15  | Cache und `nx affected` (inkl. `--graph`, `--explain`), eigenes Target mit `nx:run-commands`, `dependsOn`, `nx run-many`. Brücke zu CI (Lesson 08). |
| 70  | Architekturtest (ArchUnitTS)   | neu, **abhängig vom Spike**        | 15  | Eine Regel, die Nx nicht kann (Zyklen, Namenskonvention oder "keine `HttpClient`-Injection in Komponenten"). Abgrenzung: Nx prüft Projekt gegen Projekt, ArchUnit Dateien und Ordner. |
| 80  | Bonus: Runtime Configuration   | existiert (`04-runtime-configuration`), **umbauen**, **Bonus** | 10  | Konfiguration zur Laufzeit statt Build-Zeit (ein Build, mehrere Stages), siehe unten. |
| 90  | Bonus: Generator für ein Aggregat | neu, **Bonus**                  | 20+ | Eigener Nx-Generator, der eine Library mit Grundstruktur für ein Aggregat erzeugt (siehe unten). |

Summe ohne Bonus: **95 min** (ohne Task 70: 80 min). Mono vs. Multi Repo und weitere Themen sind reiner Folieninhalt (siehe Abschnitt "Folieninhalte ohne Task") und zählen hier nicht als Task.

### Task 10: Architecture Decision Record

Der ADR steht **am Anfang**, damit Entscheidungen im Team abgesichert sind, bevor jemand Code verschiebt.

- **Vorlage:** Der bestehende Task liefert Template und Aufbau (Status, Context, Considered Options, Decision, Consequences). Das bleibt.
- **Inhalt ändert sich:** Der bisherige Task dokumentiert Runtime Configuration. Neu dokumentieren die Teilnehmenden die Entscheidungen dieser Lesson, z. B. "Feature-basierte Struktur statt Schichten" und "Mono-Repo mit Nx statt Multi-Repo". Die Optionen kommen aus den Folien (Mono vs. Multi Repo, Tool-Überblick).
- **Warum erst Optionen, dann Entscheidung:** Das ist die Trade-off-Analyse und die beworbene Architekturarbeit im Team. Der Task kann als Teamarbeit angelegt werden (Paare einigen sich auf eine Entscheidung).
- **Wiederverwendung später:** Task 50 ergänzt die Tag-Regeln als Konsequenz. Task 80 (Bonus) nutzt dieselbe Vorlage für einen ADR zur Runtime Configuration.
- **Ablageort:** `docs/architecture/decision-records/` (Pfad im bestehenden Task, inkl. Tippfehler `achtitecture` in der Solution, prüfen und vereinheitlichen).
- **Offen:** Wie "echt" ist die Entscheidung? Entweder gibt der Task die Entscheidung vor und die Teilnehmenden begründen sie, oder sie entscheiden selbst. Zweites ist näher an der Praxis, erste ist besser planbar, weil die Folgetasks Nx voraussetzen.

### Task 60: Affected und eigene Commands

Idee: Zuerst eine Änderung in `ui` machen und mit `nx affected -t test --graph` sehen, welche Projekte betroffen sind. Danach ein eigenes Target anlegen, das die Struktur nutzt.

- Cache: zweiter Lauf von `build` oder `test` kommt aus dem Cache (`targetDefaults` mit `"cache": true`).
- `nx affected -t lint test build`: Wirkung einer Änderung in `ui` gegenüber einer Änderung in einer Feature-Library.
- `--explain` zeigt, warum ein Projekt betroffen ist.
- Eigener Command als Target in `project.json` oder `nx.json`, z. B. `nx:run-commands` für einen Check oder ein Skript. Kandidat: ein Target `check`, das über `dependsOn` Lint und Test bündelt.
- `nx run-many -t check` über alle Projekte, danach dasselbe nur affected.
- Offen: Konkretes Beispiel für den eigenen Command festlegen. Er sollte später in der CI (Lesson 08) und im Architekturtest (Task 70) wiederverwendbar sein.

### Task 80 (Bonus): Runtime Configuration

Konfiguration (z. B. API-Endpoint) wird zur Laufzeit aus einer JSON-Datei geladen, damit derselbe Build in mehreren Stages läuft. Der bestehende Task `04-runtime-configuration` bleibt inhaltlich, muss aber an die Nx-Struktur angepasst werden:

- **Letzter Schritt "Update ESLint boundaries" entfällt.** Er bezieht sich auf `eslint-plugin-boundaries` (Entscheidung E1). Ersatz: Liegt `RuntimeConfiguration` in einer eigenen Library, bekommt sie einen passenden Tag und die `depConstraints` aus Task 50 werden um ihn ergänzt.
- **Pfade:** `/assets/configuration/api-endpoints.json` und die Platzierung des Service müssen zur Nx-Struktur passen. Offen ist, in welcher Library `RuntimeConfiguration` liegt (eigene Library oder in `data-access`).
- **Abhängigkeit:** `BookApiClient` holt den Endpoint aus der Konfiguration. Der Store (Lesson 04) darf nicht von der Konfiguration abhängen, wenn der Bonus nicht gemacht wurde. Die Lessons müssen auch **ohne** den Bonus funktionieren.
- **Abschluss:** Die Teilnehmenden halten die Entscheidung als ADR fest (Vorlage aus Task 10). Das ersetzt den bisherigen Inhalt des ADR-Tasks.
- **Brücke:** Dieselbe Idee taucht beim Federation-Manifest (Lesson 07) und bei CD (Lesson 08) wieder auf.

### Task 90 (Bonus): Generator für ein Aggregat

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

Vorgehen: `nx g @nx/plugin:plugin` bzw. `@nx/plugin:generator` (zu recherchieren), Template-Dateien mit `__name__`-Platzhaltern, Schema-Optionen für `name`, `scope`. Ergebnis wird mit `nx graph` und den Boundaries aus Task 50 geprüft.

- Verknüpfung: Der Generator darf die Boundaries nicht verletzen. Sonst scheitert Task 50 am eigenen Output.
- Verknüpfung: Ein ArchUnit-Test (Task 70) kann prüfen, ob generierte Libraries der Konvention entsprechen.
- Bonus-Dateien: `bonus.md` zum Task 60 oder eigener Task-Ordner. Entscheidung bei der Umsetzung.

## Folieninhalte ohne Task

Diese Themen sind beworbene Inhalte, bekommen aber **keine Aufgabe**. Sie leben ausschließlich in den Slides (Google Slides, siehe CLAUDE.md). Die Position gibt an, wo sie im Ablauf der Tasks stehen.

| Thema                                  | Position im Ablauf         | Inhalt (Entwurf)                                                                                                           | Beworben als                           |
| -------------------------------------- | -------------------------- | -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------- |
| Architekturentscheidungen im Team      | vor Task 10                | Warum Entscheidungen festhalten, ADR-Aufbau, Trade-off-Analyse, Entscheidungen im Team absichern.                           | Trade-off-Analyse, Architekturarbeit im Team |
| Feature-/Domain-basierte Struktur      | vor Task 10                | Warum nach Feature statt nach Schicht schneiden. Domänen und Aggregate, Vertical Slices, Struktur für Enterprise-Plattformen. Liefert die Optionen für den ADR. | Feature-/Domain-basiert, Enterprise-Struktur |
| Mono vs. Multi Repo                    | vor Task 10                | Warum-Frage und Entscheidungskriterien (Team- und Codebasis-Größe, Release-Kopplung, Code-Sharing, CI-Kosten, Ownership). Diskussion statt Aufgabe. Liefert Optionen für den ADR. | Multi- vs. Monorepo                    |
| Tool-Überblick: Nx, Bit, Rush, Turborepo | nach Task 30             | Vergleich anhand dessen, was die Teilnehmenden gerade mit Nx erlebt haben (Graph, Cache, Generatoren).                     | Tool-Überblick                         |
| Library-Typen und Tag-Modell           | vor Task 50                | Konzept `type` (feature → data-access → ui) und `scope`, erlaubte Abhängigkeitsrichtung. Vorbereitung der `depConstraints`. | Architektur (Teil von Mono-Repo-Tools) |
| Grenzen der Werkzeuge                  | vor Task 70                | Was Nx Boundaries prüft (Projekt gegen Projekt) und was nicht (Dateien, Zyklen, Namen). Überleitung zum Architekturtest.   | Architektur, Codeanalyse               |
| Brücke zu CI und Governance            | nach Task 60               | Ausblick auf Lesson 08: `nx affected` und Architekturtests in der Pipeline.                                                 | CI, CD                                 |

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

- Der ADR (Task 10) steht **vor** allem anderen, weil er die Entscheidungen festhält, die die Folgetasks umsetzen.
- Project Structure muss **vor** der Nx-Migration stehen, weil die Feature-Ordner später 1:1 zu Libraries werden.
- Die in diesem Block angelegten Libraries und Tags sind die Grundlage für alle späteren Lessons (UI-Library in 03, `data-access` in 04, Affected in 08).

## Offene Fragen

- **Tag der Aggregat-Library:** Sie bündelt Page, Client und Model in einer Library, was gegen die Trennung `feature` / `data-access` / `ui` läuft. Entweder ein Tag `type:feature` mit interner Struktur oder der Generator erzeugt mehrere Libraries.
- **Eigener Command in Task 60:** Welches konkrete Target? Siehe Task 60.
- **ADR in Task 10:** Entscheidung vorgeben oder selbst treffen lassen? Siehe Task 10.
- **Runtime Configuration (Bonus):** In welcher Library liegt `RuntimeConfiguration`?
- **ArchUnitTS in 02 statt 05:** Installation ist durch E6 erledigt, offen bleibt der Spike (Pfad-Aliase, Nx-Anbindung). Bei Umzug ändern sich E5, Plan 05 und die Lesson-Tabelle im Master-Plan.
- **`nx init` standalone oder `--integrated`?** Standalone behält das Dateilayout, integrated verschiebt nach `apps/` und `libs/`. Entscheidung im Spike.
- **Welche Libraries genau?** Nur drei Typen (feature, data-access, ui) oder feiner?
- **Library-Generatoren:** Befehle für Angular-Libraries in Nx sind noch nicht recherchiert.
- Tool-Überblick: entschieden, reiner Folieninhalt (siehe "Folieninhalte ohne Task").

## Risiken

- Die Tasks summieren sich auf 95 min plus Bonus (Lesson war ursprünglich mit 75 min geplant), dazu kommt die Folienzeit. Kürzungskandidaten in dieser Reihenfolge: `scope`-Tags, Task 70, Task 60. Runtime Configuration und Generator sind Bonus.
- Vorinstallierte Pakete können mit `nx init` kollidieren (Versionen, Dopplungen). Im Spike prüfen.
- Die Nx-Migration kann je nach Angular-Version Stolpersteine haben. Ohne Spike ist die Zeitschätzung unsicher.
- Imports und Pfad-Aliase beim Verschieben sind fehleranfällig. Checkpoint-Tags nach Task 30 und 40 sind sinnvoll.
