# Master Plan: Angular Advanced Workshop

> Arbeitsdokument. Wir straffen die Anforderungen hier Schritt für Schritt.
> Status: **Entwurf v0.2**. Verbindlich ist nur, was unter "Entscheidungen" (Abschnitt 8) steht.

## 1. Ziel und Rahmen

- Der Advanced Workshop läuft im Repository `angular-advanced-workshop` (Bookmonkey-App, abgeleitet von `angular-workshop`).
- Die Teilnehmenden arbeiten über den gesamten Workshop in **einem durchgehenden Projekt**, das Schritt für Schritt wächst.
- Umfang: **5 halbe Tage** (Annahme: ca. 3 h netto pro halbem Tag, ca. 15 h gesamt. Zu bestätigen.)
- Große Würfe (Micro-Frontend, Storybook, Nx-Migration) dürfen nicht halbe Tage an Setup kosten.
- Die Solutions im `sample-solution` sind eine **lineare Commit-Kette** (ein Commit je Task). Die Reihenfolge muss stehen, bevor Solutions umgebaut werden, weil jede Umsortierung einen Rebase bedeutet.

## 2. Beworbene Inhalte (Soll)

1. **Architektur:** Überblick, Monolith vs. Modulith, Micro-Frontend, Trade-off-Analyse, Enterprise-Struktur, Feature-/Domain-basiert, Anti-Patterns, Architekturarbeit im Team
2. **Store & State:** zentral vs. dezentral, Redux/CQS, Signal Store
3. **Testing:** Vitest-Vertiefung, Testabstraktionen, Testpyramide, Component vs. Integration vs. E2E, Mock-Strategien, Testbarkeit als Architekturmerkmal
4. **Component Libraries:** CDK, Wiederverwenden statt neu erfinden, Drittanbieter evaluieren, UI/UX-Pitfalls, eigene dokumentierte Library
5. **Mono Repositories:** Multi- vs. Monorepo, Tool-Überblick (Nx, Bit, Rush, Turborepo), Architektur, Codeanalyse, CI, CD

## 3. Ist-Stand im Repo

| Lesson (Ordner)                         | Tasks vorhanden                                                                                                      |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `01-angular-advanced-introduction`      | Preparation, Choose your lessons and focus                                                                           |
| `02-architecture`                       | Project Structure, Enforce Module Boundaries (mit `eslint-plugin-boundaries`), Runtime Configuration, ADR            |
| `03-signal-store`                       | Setup, RxJS Streams, Lifecycle Hooks, Computed State, Custom Store Feature, Third Party Plugins, Entity Management, Events |
| `07-angular-testing`                    | Testbed, Testing Library, Mock service, Mock components, Http                                                        |
| `08-component-libraries` (nur Skelett)  | 4 leere `task.yml` aus der ersten Runde                                                                              |
| `09-mono-repositories` (nur Skelett)    | 5 leere `task.yml` aus der ersten Runde                                                                              |

## 4. Entdeckte Abhängigkeiten zwischen bestehenden Tasks

1. **Project Structure** ist Voraussetzung für Libraries: Die Feature-Ordner und `data/` werden später 1:1 zu Nx-Libraries, der Alias `@common-components` zum Library-Import.
2. **Enforce Module Boundaries** nutzt heute `eslint-plugin-boundaries` auf Ordnerebene. Wird auf **Nx-Tags** (`@nx/enforce-module-boundaries`) umgebaut und kommt erst nach der Library-Extraktion.
3. **Runtime Configuration** (jetzt Bonus in Lesson 02) hat als letzten Schritt "Update ESLint boundaries". Dieser Schritt muss entkoppelt werden. Inhaltlich ist der Task Vorwissen für das Micro-Frontend (Manifest zur Laufzeit) und für CD (ein Build, mehrere Stages).
4. **ADR** steht jetzt **am Anfang** von Lesson 02 (E7) und dokumentiert die Strukturentscheidungen. Der ADR zur Runtime Configuration wird Teil des Bonus.
5. **Store** kommt nach der Library-Extraktion und landet direkt in einer `data-access`-Library statt später verschoben zu werden.
6. **Testing** kommt nach dem Store, damit es Komponenten, Services, Store und UI-Library zum Testen gibt. Die Tests laufen dann unter `nx test`.
7. **Micro-Frontend** braucht Libraries und Boundaries als Vorwissen. Runtime Configuration ist nur Bonus, der Manifest-Gedanke muss deshalb im Micro-Frontend selbst eingeführt werden.

## 5. Leitidee: ein durchgehendes Projekt

```
Bookmonkey-App
  → feature-basierte Struktur
  → Nx-Migration → Libraries → Boundaries (Tags)
  → Component Library (CDK, Aria, Storybook, Browser-Tests)
  → Store (data-access-Library)
  → Testing (Unit bis E2E)
  → ADR (Entscheidungen festhalten, Start von Lesson 02)
  → Runtime Configuration (Bonus)
  → Micro-Frontend (Native Federation)
  → CI/CD, Codeanalyse, Anti-Patterns
```

Jeder Schritt setzt den vorherigen voraus, ist aber über **Checkpoints** (Git-Tags) jederzeit neu einsteigbar.

## 6. Lesson-Schnitt (nach Thema)

| #   | Lesson                                | Tasks (**fett** = existiert schon, ggf. angepasst)                                                                                         | Min |
| --- | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ | --- |
| 01  | Introduction                          | **Preparation**, **Choose your lessons and focus**                                                                                         | 25  |
| 02  | Architecture: Decisions, Structure & Mono-Repo | **ADR** (nach Folien zu Entscheidungen, Mono vs. Multi Repo) → **Project Structure** → Nx Migration (danach Folien Tool-Überblick) → Libraries extrahieren → **Enforce Module Boundaries** (auf Nx umgebaut) → Affected und eigene Commands → Bonus: **Runtime Configuration** → Bonus: Generator für ein Aggregat | 80  |
| 03  | Component Libraries                   | Autocomplete (Material/CDK) → generische Entity-Auswahl → Host-Directive → Einsatz → Storybook → Browser-Tests (Bonus: Aria, Routed Dialog)   | 110 |
| 04  | State                                 | Zentral vs. dezentral (neu) → **Signal Store 1–8**                                                                                         | 110 |
| 05  | Testing                               | **Testbed** (Einstieg) → Vitest-Refactoring (`expect.poll`, `expect.element`) → Vitest Browser Mode → **Testing Library** → **Mock service, Mock components, Http** → Testpyramide und Testbarkeit → Architekturtests (ArchUnitTS, aus 02 verlegt) → E2E (Playwright) | 160 |
| 06  | *entfallen*, aufgegangen in Lesson 02 (E7)                                                                                                | 0   |
| 07  | Micro-Frontend                        | Monolith vs. Modulith vs. Micro-Frontend → Remote anbinden → Shared Dependencies                                                           | 45  |
| 08  | Delivery & Governance                 | CI mit `nx affected` → Codeanalyse → CD → Anti-Patterns finden                                                                             | 50  |

**Summe ca. 580 min, also etwa 9,7 h reine Aufgabenzeit bei 15 h Workshop.** Lesson 05 ist mit 160 min die größte Lesson, Lesson 02 hat 80 min. Die Folienzeit kommt dazu. Kürzungskandidaten stehen in Detailplan 05. Das lässt Reserve für Folien, Diskussion, Pausen und Hänger.

### Details zu neuen Tasks

**02 Mono-Repo (Nx)**

| Task                | Inhalt                                                                                   | Min |
| ------------------- | ---------------------------------------------------------------------------------------- | --- |
| Nx Migration        | `nx init`, Ergebnis lesen (`nx.json`, `project.json`), `nx graph`                        | 15  |
| Libraries           | Feature-Ordner und `data/` zu Libraries machen, Imports anpassen                         | 15  |
| Module Boundaries   | Tags, `depConstraints`, bewusster Verstoß und Fehlermeldung                              | 15  |
| Affected und eigene Commands | Cache, `nx affected`, eigenes Target (`nx:run-commands`, `dependsOn`), `nx run-many`, Brücke zu CI | 15  |
| Bonus: Generator    | Nx-Generator bootstrapt Library für ein Aggregat (routing, client, page, model, Valibot-Schema) | 20+ |

**03 Component Libraries**

| Task                             | Inhalt                                                                          | Min |
| -------------------------------- | ------------------------------------------------------------------------------- | --- |
| Autocomplete mit Material/CDK    | Baustein aus Library-Komponenten statt Eigenbau ("Wiederverwenden statt neu erfinden") | 20  |
| Generische Entity-Auswahl        | Abstraktion: eine Komponente für Buch und Autor, Suche und Anzeige als Input   | 20  |
| Host-Directive                   | Verhalten in Direktiven auslagern, per `hostDirectives` komponieren             | 15  |
| Einsatz in Feature und Boundaries | `ui` ohne `data-access`, Daten kommen aus der Feature-Library                  | 15  |
| Storybook                        | Setup per Generator, Story als Dokumentation                                    | 20  |
| Browser-Tests                    | Tests gegen Stories oder im echten Browser                                      | 15  |
| Bonus: Angular Aria              | headless Variante der Auswahl mit eigenem Styling                               | 20  |
| Bonus: Routed Dialog             | Dialog über eigenen Router-Outlet; Musterlösung zeigt Umsetzung. Entity-Auswahl-Ausblick (Dialog, Anlegen) nur Folien | 20  |

Der Library-Vergleich (Spartan NG, PrimeNG, Material, DevExpress, Angular Aria) ist reiner Folieninhalt. Die Libraries `ui`, `data-access` und eine Feature-Library werden bereits in Lesson 02 leer angelegt (siehe Detailplan 03).

**04 State:** nur ein neuer Task. Die bestehenden Signal-Store-Tasks werden auf die `data-access`-Library gemappt, nicht neu geschrieben. Kürzungspotenzial prüfen.

**05 Testing:** neu sind Vitest-Refactoring (10), Vitest Browser Mode (15), Testpyramide/Testbarkeit (15), **Architekturtests (20, aus Lesson 02 verlegt)** und E2E (20). Der Testbed-Task bleibt als Einstieg für Teilnehmende ohne Testerfahrung, die Testing Library kommt erst nach den Vitest-Tasks. Details zu den Architekturtests (Werkzeug, Abgrenzung zu Nx Boundaries, Regelkandidaten) stehen im [Detailplan 05](./05-testing.md).

### Detailpläne je Lesson

Jede Lesson bekommt einen eigenen Plan, in dem wir die Tasks vertiefen. Der MASTER-PLAN behält Schnitt, Reihenfolge und Entscheidungen.

| #   | Detailplan                                                                      |
| --- | ------------------------------------------------------------------------------- |
| 01  | [01-introduction.md](./01-introduction.md)                                      |
| 02  | [02-architecture-structure-and-mono-repo.md](./02-architecture-structure-and-mono-repo.md) |
| 03  | [03-component-libraries.md](./03-component-libraries.md)                        |
| 04  | [04-state.md](./04-state.md)                                                    |
| 05  | [05-testing.md](./05-testing.md)                                                |
| 06  | [06-architecture-configuration-and-decisions.md](./06-architecture-configuration-and-decisions.md) |
| 07  | [07-micro-frontend.md](./07-micro-frontend.md)                                  |
| 08  | [08-delivery-and-governance.md](./08-delivery-and-governance.md)                |

**07 Micro-Frontend**

| Task                                     | Inhalt                                                          | Min |
| ---------------------------------------- | --------------------------------------------------------------- | --- |
| Monolith vs. Modulith vs. Micro-Frontend | Konzept und Trade-offs, ohne Code                               | 10  |
| Remote anbinden                          | Remote liegt fertig vor, Host: Manifest, Route                  | 20  |
| Shared Dependencies                      | Singleton, Versionskonflikt provozieren und lösen               | 15  |

**08 Delivery & Governance:** CI (15), Codeanalyse (10), CD (10), Anti-Patterns (15).

## 7. Didaktik für große Würfe: Scaffold, Verstehen, Eigene Änderung

1. **Scaffold (2–5 min):** Generator-Befehl oder Checkout eines Tags. Kein Handarbeits-Setup.
2. **Verstehen (ca. 5 min):** Gezielte Leitfrage zum Generierten.
3. **Eigene Änderung (5–10 min):** Kleine Änderung, die Verständnis beweist.

Weitere Hebel:

- **Checkpoints:** pro Aufgabe ein Start-Tag (`git_tag_starting`) für alle, die hängen.
- **Vorgefertigte Teile:** z. B. der Remote des Micro-Frontends liegt fertig bereit.
- **Trainer-Live-Demo** für einmalige Setups, danach alle auf einem gemeinsamen Tag-Stand.
- **Vorgewärmte Umgebung:** `node_modules`, Nx-Cache, Playwright-Browser vorab (Preparation-Task).
- **Bonus** für alles Setup-lastige, das nicht zentral ist.

## 8. Entscheidungen

| #   | Entscheidung                                                                                                                                  | Datum      |
| --- | --------------------------------------------------------------------------------------------------------------------------------------------- | ---------- |
| E1  | Module Boundaries nur mit Nx (`@nx/enforce-module-boundaries`). Die ESLint-Variante (`eslint-plugin-boundaries`) entfällt, auch als Bonus.    | 2026-11-16 |
| E2  | Lessons werden **nach Thema** geschnitten, nicht nach Tag.                                                                                    | 2026-11-16 |
| E3  | Testing kommt **nach** dem Store.                                                                                                             | 2026-11-16 |
| E4  | Micro-Frontend ist ein **eigener Lesson-Block**. Thematisch ist es Architektur, braucht aber Vorwissen und steht deshalb spät im Ablauf.      | 2026-11-16 |
| E6  | `@nx/angular`, ArchUnitTS (`archunit`) und `valibot` sind im Workshop-Repo **vorinstalliert**. Kein Install in der Aufgabenzeit, Aufwärmen in der Preparation. | 2026-11-19 |
| E7  | **ADR steht am Anfang von Lesson 02**, damit Architekturentscheidungen im Team abgesichert sind, bevor umgebaut wird. **Runtime Configuration ist Bonus** in Lesson 02. Lesson 06 entfällt. | 2026-11-19 |
| E8  | Formularanbindung der generischen UI-Komponente (Lesson 03) per **Signal Forms**, nicht ControlValueAccessor. | 2026-11-20 |
| E9  | `@angular/material` und `@angular/aria` (plus Storybook-/Browser-Test-Pakete nach Recherche) werden im Workshop-Repo **vorinstalliert**. | 2026-11-20 |
| E11 | Architekturtests liegen in **Lesson 05**, nicht in 02: Teilnehmende ohne Testerfahrung wären in 02 überfordert. Lesson 05 startet mit einem TestBed-Test als Einstieg, danach Vitest-Mittel (`expect.poll`, `expect.element`, Browser Mode), dann Testing Library. | 2026-11-20 |
| E10 | Die Libraries `ui`, `data-access` und eine Feature-Library werden in Lesson 02 **leer angelegt**. Lesson 03 füllt `ui`. | 2026-11-20 |

**E5 (2026-11-16):** Architekturtests gehören in die Testing-Lesson (05). Werkzeugkandidat ist ArchUnitTS. Welche Regeln wir lehren, klärt der [Detailplan 05](./05-testing.md). Ein Umzug nach Lesson 02 war erwogen, E11 bestätigt Lesson 05.

## 9. Recherche-Stand zu den Quellen

Gelesen am 2026-11-16 (nx.dev, native-federation.com). Befehle sind aus den Docs übernommen, aber **noch nicht praktisch durchgespielt**.

### Nx

| Thema                  | Befehl / Fakt                                                                                                                                    |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| Migration (Standalone) | `npx nx@latest init` (behält Dateilayout, erzeugt `nx.json` und je Projekt `project.json`)                                                       |
| Migration (Monorepo)   | `npx nx@latest init --integrated` (verschiebt nach `apps/` und `libs/`, installiert `@nx/angular`)                                               |
| Prüfen                 | `nx serve/build/test <app>`, `nx graph`                                                                                                          |
| Boundaries             | Tags in `project.json`, `nx add @nx/eslint-plugin @nx/devkit`, Regel `@nx/enforce-module-boundaries` mit `depConstraints` in `eslint.config.mjs` |
| Caching                | `nx.json` → `targetDefaults` mit `"cache": true`                                                                                                 |
| Affected / CI          | `nx affected -t build test`, `--graph`, `--explain`; GitHub Actions mit `nrwl/nx-set-shas`, `fetch-depth: 0`                                     |

### Native Federation

| Thema              | Fakt                                                                                                                        |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| Idee               | Module-Federation-Modell mit ESM und Import Maps, bundler-unabhängig (esbuild)                                              |
| Paketname          | ab Angular 22 `@angular-architects/native-federation`, auf Angular 20/21 `@angular-architects/native-federation-v4`         |
| Voraussetzung      | Application Builder (`@angular/build:application`)                                                                          |
| Remote             | `ng g @angular-architects/native-federation:init --project mfe1 --port 4201 --type remote`                                  |
| Host               | `ng g ...:init --project shell --port 4200 --type dynamic-host`                                                             |
| Verdrahtung        | `federation.manifest.json` (Remote-Name → `remoteEntry.json`) plus lazy Route mit `loadRemoteModule('mfe1', './Component')` |
| Generierte Dateien | `federation.config.mjs`, `tsconfig.federation.json`, `main.ts` mit `initFederation`, `bootstrap.ts`                         |

### ArchUnitTS (Architekturtests)

| Thema        | Fakt (README gelesen, nicht ausprobiert)                                                                       |
| ------------ | -------------------------------------------------------------------------------------------------------------- |
| Herkunft     | von ArchUnit inspiriert, **kein offizieller Port**, eigenständige Bibliothek                                   |
| Paket        | `npm install archunit --save-dev`                                                                              |
| Vitest       | funktioniert, `globals: true` in `vitest.config.ts` nötig, Matcher `toPassAsync()`                             |
| Regeln       | Abhängigkeiten (`dependOnFiles`), Zyklen (`haveNoCycles`), Namensregeln, Metriken (`metrics().count()...`)     |
| Unklar       | Umgang mit Pfad-Aliasen, Nx-Anbindung, Angular-Beispiel im Repo (nicht gelesen)                                |

### Lücken der Recherche (offen)

- [ ] **ArchUnitTS in Nx mit Angular-Pfad-Aliasen:** Funktioniert die Auflösung? Spike nötig.
- [ ] **Nx + Native Federation zusammen:** In den Docs steht nichts zur Host/Remote-Verdrahtung in Nx-Workspaces. **Muss einmal komplett durchgespielt werden.**
- [ ] **Library-Generatoren in Nx** (Angular): nicht gelesen.
- [ ] **Storybook für Angular in Nx:** nicht gelesen.
- [ ] **Library-Vergleich für Lesson 03** (Material, CDK, Angular Aria, PrimeNG, DevExpress, Spartan NG): Web-Recherche nötig, siehe Detailplan 03.
- [ ] **Angular Aria:** Reifegrad und Umfang (Web-Recherche).
- [ ] **Material/CDK Autocomplete, Signal-Forms-Custom-Control und `hostDirectives`:** Web-Recherche.
- [ ] **Browser-Tests gegen Stories** (Vitest Browser Mode oder Playwright in Nx): Web-Recherche.
- [ ] **Native-Federation-Runtime-Seite:** Die Docs raten von `loadRemoteModule` als Top-Level-Funktion ab und empfehlen den Loader über DI. Entscheidung nötig, welchen Weg wir lehren.
- [ ] Welche Angular-Version nutzt das Workshop-Repo? Davon hängt der Paketname (`-v4` oder nicht) ab.

## 10. Umbau-Aufwand (bestehende Ordner)

| Heute                                  | Künftig                                                    | Hinweis                                                                |
| -------------------------------------- | ---------------------------------------------------------- | ---------------------------------------------------------------------- |
| `02-architecture` (4 Tasks)            | bleibt Lesson 02                                           | ADR (nach vorn), Project Structure, Boundaries (umgebaut), Runtime Config (Bonus) |
| `03-signal-store`                      | `04-…`                                                     | nur Position/Ordnername, Inhalte bleiben                               |
| `07-angular-testing`                   | `05-…`                                                     | nur Position/Ordnername                                                |
| `08-component-libraries` (Skelett)     | `03-…`                                                     | Skelett an Abschnitt 6 anpassen                                        |
| `09-mono-repositories` (Skelett)       | aufgelöst: Tasks gehen nach 02 und 08                      | Skelett wird entfernt                                                  |
| neu                                    | `07-micro-frontend`, `08-delivery-and-governance`          |                                                                        |

Zusätzlich betroffen: Slides (die Architektur-Lesson hat Slidev-Folien und müsste geteilt werden), `git_tag_starting`/`git_tag_completed` je Task, die lineare Commit-Kette im `sample-solution`.

## 11. Offene Fragen

1. **Angular-Version** des Workshop-Repos?
2. **Wo liegen die Checkpoints?** Weiter im `sample-solution`-Submodul oder zusätzlich eigene Tags?
3. **Micro-Frontend-Remote:** als Ordner im Repo oder aus der Hilfsbibliothek?
4. **Enterprise UX Patterns:** entschieden. Nur Folienausblick (Entity-Auswahl mit Dialog und Anlegen), Routed Dialog als Bonus-Task (siehe Detailplan 03).
5. **Tag-Länge:** stimmen ca. 3 h netto pro halbem Tag?
6. **Tool-Überblick** (Nx, Bit, Rush, Turborepo): reine Folien oder auch Mini-Vergleich als Task?
7. ~~Lesson 06 zu klein~~: entschieden (E7), aufgegangen in Lesson 02. Offen: Lesson-Nummern 07 und 08 umnummerieren?
8. **Zentral vs. dezentral:** reicht ein kurzer Task, oder zählt das als Folienthema?

## 12. Nächste Schritte

1. Offene Fragen klären, Entscheidungen ergänzen.
2. **Spike:** den kompletten Weg einmal praktisch durchspielen (Nx-Migration, Libraries, Boundaries, Storybook, Native Federation). Bestätigte Befehle und Stolpersteine in Abschnitt 9 festhalten.
3. Ordner umbauen (Abschnitt 10) und Task-Skelette anlegen.
4. Solutions im `sample-solution` neu ordnen (Rebase).
5. Erst danach `body.md` und `hint.md` der neuen Tasks.
