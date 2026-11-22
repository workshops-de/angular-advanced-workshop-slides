# Lesson 02 Architecture: Slide-Outline für Google Slides

> Grundlage: Slidev-Deck `slidev-slides/lessons/02-architecture/slides/architecture/topic.md` (68 Folien) abgeglichen mit [02-architecture-structure-and-mono-repo.md](./02-architecture-structure-and-mono-repo.md).
> Folientexte sind Englisch (Skill `slide-creator`), Erläuterungen Deutsch.
> Konventionen: max. 4 Bullets, je max. 70 Zeichen, einzeiliger Titel, Layout nach didaktischer Funktion.
> Legende Herkunft: **E** = existiert im Slidev-Deck (übernehmen), **M** = existiert, wird verschoben, **N** = neu (aus Plan), **T** = Task-Folie.

---

## 1. Inventar des Slidev-Decks (nichts darf entfallen)

| #   | Block                    | Inhalt                                                                                                                                                                                                                                                                                                                                                                                                                  |
| --- | ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A   | Intro                    | Section "Architecture"; Why Architecture Matters (4 Probleme); What is Architecture (Project-Structure → Release-Strategy)                                                                                                                                                                                                                                                                                              |
| B   | Project/Code-Structure   | Why (Screaming Architecture); Options (feature vs. layer, Monorepo vs. Multi-Repo, Libraries, Microfrontends); Feature- vs. Layer-based (Bild); Feature-based Structure (Tree, `public-api.ts`); `public-api.ts` vs `index.ts`; Cyclic Dependencies (Mermaid); `public-api.ts` + Path Aliases (tsconfig); Use-Case-based Structure (Tree mit `data/`, `features/`, Shells); tldraw-Diagramm; **Task Project Structure** |
| C   | Component Architecture   | Why; Smart vs. Dumb; Data-Flow-Diagramm (Mermaid); tldraw-Diagramm                                                                                                                                                                                                                                                                                                                                                      |
| D   | State Management         | Why; Options (Signal Store, Redux, RxJS, CQS); Considerations; Bild "Types of UI State"                                                                                                                                                                                                                                                                                                                                 |
| E   | Layering                 | Why; Typical Layers; UI→Facade→Domain→API (Mermaid); Bild Facade Pattern                                                                                                                                                                                                                                                                                                                                                |
| F   | Module/Domain Boundaries | Why; Enforcing (Sheriff, eslint-plugin-boundaries); Bild Enforcing Module Boundaries; **Task Enforce Module Boundaries**                                                                                                                                                                                                                                                                                                |
| G   | Performance Architecture | Why; Techniques; Checklist; tldraw-Diagramm                                                                                                                                                                                                                                                                                                                                                                             |
| H   | Build & Deployment       | Why; Topics (Nx, CI/CD, Microfrontends, Release); Promote Artifacts; tldraw-Diagramm; **Task Runtime Configuration**                                                                                                                                                                                                                                                                                                    |
| I   | ADRs                     | Why; What are ADRs; Template (Code); What happens without; Outlook ADRs + AI; **Task ADR**                                                                                                                                                                                                                                                                                                                              |
| J   | Nx in a nutshell         | Why Nx; What is Nx; Core Building Blocks (+ Graph-Bild); When Nx helps / might not fit; Use Nx when / Skip Nx when                                                                                                                                                                                                                                                                                                      |

## 2. Abgleich mit dem Plan

| Plan-Inhalt                                                             | Im Deck?                                                  | Konsequenz                                                                                |
| ----------------------------------------------------------------------- | --------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| Task 10 ADR **am Anfang**, Inhalt: Struktur + Mono-Repo                 | ADR-Block steht am Ende, Task dokumentiert Runtime Config | **M** nach vorn, Task-Folie auf neues Thema                                               |
| Folie "Architekturentscheidungen im Team" (Trade-off)                   | teilweise (ADR Why/Template)                              | **N**: Trade-off-Analyse, Entscheidungen im Team                                          |
| Feature-/Domain-basiert, Enterprise-Struktur                            | Feature vs. Layer, Screaming, Use-Case-Struktur           | E + **N**: Domains/Aggregate, Vertical Slices, Enterprise                                 |
| Mono vs. Multi Repo (Kriterien)                                         | nur eine Zeile in "Options"                               | **N**: Entscheidungskriterien-Folien                                                      |
| Task 20 Project Structure vor Nx                                        | vorhanden                                                 | E, unverändert                                                                            |
| Task 30 Nx Migration                                                    | nur Nx-Theorie                                            | E (Theorie) + **N** Task-Folie, `nx init`, Graph, Cache-Appetizer                         |
| Tool-Überblick Nx/Bit/Rush/Turborepo                                    | fehlt                                                     | **N** nach Task 30                                                                        |
| Task 40 Libraries (`feature-*`, `data-access`, `ui`), bewusster Verstoß | Layering-Folien als Theorie                               | E + **N** Task-Folie                                                                      |
| Library-Typen und Tag-Modell (`type`, `scope`)                          | fehlt                                                     | **N** vor Task 50                                                                         |
| Task 50 Boundaries mit Nx (statt `eslint-plugin-boundaries`, E1)        | Sheriff + eslint-plugin-boundaries                        | **E bleibt** (als Alternativen), Fokus wechselt auf Nx, siehe Konflikt K1                 |
| Grenzen der Werkzeuge (Ausblick Architekturtest Lesson 05)              | fehlt                                                     | **N**                                                                                     |
| Task 60 Affected + eigene Commands                                      | fehlt                                                     | **N**: Cache, `affected`, `--graph`, `--explain`, `run-commands`, `dependsOn`, `run-many` |
| Brücke zu CI/Governance (Lesson 08)                                     | "CI/CD pipelines" nur als Stichwort                       | **N**                                                                                     |
| Bonus Task 80 Runtime Configuration                                     | Task + "Promote Artifacts"                                | E, an Nx-Struktur anpassen (Tag statt Boundaries-Update)                                  |
| Bonus Task 90 Generator                                                 | fehlt                                                     | **N** Bonus-Task-Folie + What-If                                                          |
| Component Architecture, State Management, Performance                   | nicht im Plan                                             | **E bleiben** (nichts entfernen), siehe K2                                                |

### Konflikte und offene Punkte

- **K1 (entschieden):** Alle Ansätze werden gegenübergestellt: Sheriff, eslint-plugin-boundaries und Nx. Zusätzlich ein Syntaxvergleich eslint-plugin-boundaries vs. Nx (Folien 71a bis 71c), damit sichtbar wird, wie ähnlich die Regeln sind.
- **K2 (entschieden):** Component Architecture, State Management, Layering und Performance bekommen keinen Task, werden aber vollständig erklärt. Viele Teilnehmende kennen die Prinzipien, sie sind trotzdem zentral. Die Folien bekommen daher je eine Main-Point-Folie mit der Kernregel. Die frühere Annahme "80 min" stammt aus der Task-Summe im Plan (nur Aufgabenzeit, ohne Theorie) und ist keine Gesamtdauer.
- **K3 (entschieden):** Facade wird in den Slides diskutiert, nicht übergangen. Folien 51a bis 51d: was eine Facade ist, wann sie sich lohnt, was sie kostet, und warum der Signal Store ihre Bedeutung abschwächt. Verbindung zu Lesson 04.
- **K4:** "Mono vs. Multi" und "Microfrontends" stehen in einer Zeile in "Project Structure Options". Beide bekommen echte Folien (Mono/Multi hier, Microfrontends Verweis auf Lesson 07).
- **K5:** Bilder aus Slidev (`feature-vs-layer-based.png`, `nx-dependency-graph.png`, `enforcing-module-boundaries.png`, `types-of-ui-state.png`, `facade-pattern-angular-architecture.png`) und vier tldraw-Diagramme müssen exportiert/neu gezeichnet werden.
- **K6 (gelöst):** `google_slide_id` steht in `lessons/02-architecture-structure-monorepositories/lesson.yml` (`1xguyWc-kOnPFz4b-gS74qzXyvYiRBRCgdaadEkKvvr4`). Das Ziel-Deck existiert schon, siehe Abschnitt 8.

---

## 3. Neue Dramaturgie

Entscheiden → Konvention → sichtbar → physisch → erzwungen → genutzt (Plan). Das Deck folgt den Tasks:

| Teil | Titel                                        | Task                |
| ---- | -------------------------------------------- | ------------------- |
| 1    | Intro                                        | –                   |
| 2    | Options: Structure & Repo                    | –                   |
| 3    | Decisions: ADR                               | **10** (ca. 10 min) |
| 4    | Project Structure                            | **20** (10 min)     |
| 5    | Nx in a Nutshell + Migration                 | **30** (15 min)     |
| 6    | Libraries, Layers & Tags                     | **40** (15 min)     |
| 7    | Building Blocks (Component, State)           | – (Theorie)         |
| 8    | Enforce Module Boundaries                    | **50** (15 min)     |
| 9    | Affected & Custom Commands                   | **60** (15 min)     |
| 10   | Further Topics (Performance, Build & Deploy) | **80** Bonus        |
| 11   | Generator                                    | **90** Bonus        |
| 12   | Wrap-up                                      | –                   |

---

## 4. Detail-Outline (Folie für Folie)

### Teil 1: Intro

| #   | Layout              | Titel                    | Inhalt                                                                                                                                                                                                       | Her. |
| --- | ------------------- | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---- |
| 1   | Title Slide         | Architecture             | Important Principles, Possibilities & Techniques                                                                                                                                                             | E    |
| 2   | Why                 | Why Architecture Matters | Intro-Satz (team agrees on repetitive patterns, clear concepts and boundaries). Bullets: Messy codebases without structure / Hard onboarding / Slow builds, unclear dependencies / Unclear domain boundaries | E    |
| 3   | Little What         | What is Architecture?    | From Project-Structure to Release-Strategy                                                                                                                                                                   | E    |
| 4   | What If? (optional) | Who decides?             | Frage: How do we keep decisions alive in a team? Teaser auf ADR und Nx (Roter Faden "Decision → Implementation → Enforcement")                                                                               | N    |

### Teil 2: Options (Input für den ADR)

| #   | Layout         | Titel                              | Inhalt                                                                                                                                                                      | Her. |
| --- | -------------- | ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---- |
| 5   | Section header | Project/Code-Structure             | –                                                                                                                                                                           | E    |
| 6   | Why            | Why Project Structure Matters      | Predictable navigation, less cognitive load. Screaming Architecture (Robert C. Martin): folders reveal what the app does                                                    | E    |
| 7   | Little What    | Project Structure Options          | Feature-based vs. layer-based. Monorepo vs. multi-repo. Libraries and shared modules. Microfrontends                                                                        | E    |
| 8   | What (Bild)    | Feature-based vs. Layer-based      | Bild `feature-vs-layer-based.png`; 3 Bullets: feature-based, layer-based, Screaming Architecture                                                                            | E    |
| 9   | Why            | Why Slice by Feature?              | Changes stay local; ownership per feature; deletable features; scales with team size                                                                                        | N    |
| 10  | What           | Domains and Aggregates             | Domain = business area, aggregate = consistency unit; one aggregate maps to one feature slice                                                                               | N    |
| 11  | What           | Vertical Slices                    | Slice goes from UI to data; layers live inside the slice. Bezug zur Use-Case-Struktur (Teil 4)                                                                              | N    |
| 12  | What           | Structure for Enterprise Platforms | Many teams, many domains; clear ownership, public APIs, shared kernel kept small                                                                                            | N    |
| 13  | Section header | Mono vs. Multi Repo                | –                                                                                                                                                                           | N    |
| 14  | Why            | Why This Is a Decision             | Repo layout shapes releases, CI cost and collaboration; hard to change later                                                                                                | N    |
| 15  | What           | Criteria                           | Team and codebase size / Release coupling / Code sharing / CI cost and ownership                                                                                            | N    |
| 16  | What           | Mono vs. Multi: Trade-offs         | Zwei Spalten (Two Cols): Mono (easy sharing, atomic changes, shared tooling; needs tooling, bigger CI) vs. Multi (autonomy, small repos; version drift, duplicated tooling) | N    |
| 17  | What If?       | Which fits your team?              | Diskussion: Teilnehmende ordnen ihr Projekt anhand der Kriterien ein                                                                                                        | N    |

### Teil 3: Decisions (ADR, Task 10)

Der Block wird aus dem Ende des Decks nach vorn verschoben (M).

| #   | Layout                  | Titel                                | Inhalt                                                                                                                                                                    | Her.                  |
| --- | ----------------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| 18  | Section header          | Documenting Architecture Decisions   | –                                                                                                                                                                         | M                     |
| 19  | Why                     | Why Document Decisions               | Decisions get forgotten. ADRs capture context and rationale for future readers                                                                                            | M                     |
| 20  | Why                     | Decisions in a Team                  | Entscheidungen absichern, Trade-off-Analyse, gemeinsame Verantwortung statt Einzelmeinung                                                                                 | N                     |
| 21  | What                    | Trade-off Analysis                   | Options → criteria → consequences. Es gibt keine "beste" Lösung, nur Kontext                                                                                              | N                     |
| 22  | Little What             | Architecture Decision Records (ADRs) | Lightweight docs: Context, Decision, Consequences                                                                                                                         | M                     |
| 23  | Code Window Explanation | ADR Template                         | Template-Code (Status, Context, Considered Options, Decision, Consequences) mit Step-Highlighting; rechts: one ADR ≈ one significant decision, written for future readers | M                     |
| 24  | Why                     | What Happens Without ADRs?           | Confused team / Knowledge lost / Impact unclear / Less motivation to change                                                                                               | M                     |
| 25  | Task                    | Architecture Decision Record         | Titel wie GitHub-Issue. Hinweis: Paare dokumentieren "Feature-based structure instead of layers" und "Mono-repo with Nx instead of multi-repo"                            | T (E, Thema geändert) |
| 26  | Little What             | Outlook: ADRs + AI                   | Arc42 aus ADRs generieren, AI nutzt ADRs als Kontext (kann hinter Task bleiben, oder in Wrap-up)                                                                          | M                     |

### Teil 4: Project Structure (Task 20)

| #   | Layout                  | Titel                             | Inhalt                                                                                                                                                            | Her. |
| --- | ----------------------- | --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---- |
| 27  | Code Window Explanation | Feature-based Structure           | Tree `src/app/` (users, orders, ui, app.ts) mit Step-Highlighting; Callout: all code that belongs to a feature lives in its directory                             | E    |
| 28  | Little What             | public-api.ts vs index.ts         | Barrel files, 2 Bullets: public-api.ts explicit; index.ts common but pulls in internals                                                                           | E    |
| 29  | Code Window Explanation | Cyclic Dependencies with index.ts | Diagramm feature-a ↔ feature-b; Callout zum Zyklus (statt Mermaid als Bild bauen)                                                                                 | E    |
| 30  | Code Window Explanation | public-api.ts + Path Aliases      | tsconfig-Code (`@users`, `@orders`, `@ui`) rechts; Bullets: imports resolve to public API only / no accidental internals / clear boundaries / tooling can enforce | E    |
| 31  | Code Window             | Use-Case-based Structure          | Quote Screaming Architecture; Tree `users/data`, `users/features/*`, `user-shell.ts`, `app-shell.ts` mit Step-Highlighting                                        | E    |
| 32  | What (Diagramm)         | Structure Overview                | tldraw-Diagramm neu zeichnen                                                                                                                                      | E    |
| 33  | Task                    | Project Structure                 | Slices mit `BookShell`, `features/*`, `data/`                                                                                                                     | T    |

### Teil 5: Nx in a Nutshell + Migration (Task 30)

| #   | Layout                  | Titel                            | Inhalt                                                                                                                                                                          | Her. |
| --- | ----------------------- | -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---- |
| 34  | Section header          | Nx in a Nutshell                 | –                                                                                                                                                                               | E    |
| 35  | Why                     | Why Nx Matters                   | Platform for scaling development. Bullets: faster CI (affected, caching) / consistent tooling / dependency visibility / worth evaluating                                        | E    |
| 36  | Little What             | What Is Nx?                      | Optimizes builds, scales CI, recovers from failed PRs; project intelligence + task orchestration on top of Angular tooling                                                      | E    |
| 37  | What                    | Core Building Blocks             | Projects, Targets, Executors, Generators, Project graph (max. 4 Bullets: Projects+Targets zusammenfassen, Executors+Generators zusammenfassen) + Bild `nx-dependency-graph.png` | E    |
| 38  | Section header          | When to Use Nx, When Not         | –                                                                                                                                                                               | E    |
| 39  | Why                     | When Nx Helps                    | Sharing libraries, long build times, full-stack platform, you know the ecosystem                                                                                                | E    |
| 40  | Little What             | When Nx Might Not Fit            | Only library extraction, Angular + Storybook/Playwright without indirection, fewer layers for faster upgrades                                                                   | E    |
| 41  | What                    | Use Nx when... / Skip Nx when... | Zwei Spalten mit je 3-4 Punkten (gleicher Inhalt wie Slidev)                                                                                                                    | E    |
| 42  | How                     | Migrating with `nx init`         | Schritte: run `nx init`, read `nx.json` and `project.json`, open `nx graph`. Hinweis: `@nx/angular` ist vorinstalliert, kein npm install                                        | N    |
| 43  | Code Window Explanation | Reading nx.json                  | Snippet `targetDefaults` mit `cache: true`; rechts Erklärung (Plan: Cache-Appetizer)                                                                                            | N    |
| 44  | Task                    | Nx Migration                     | `nx init`, Ergebnis lesen, `nx graph`, erster Cache-Treffer                                                                                                                     | T    |
| 45  | What                    | Tool Overview                    | Vergleich Nx, Bit, Rush, Turborepo anhand Graph, Cache, Generators (Tabelle oder 4 Karten)                                                                                      | N    |
| 46  | What If?                | Does it fit your setup?          | Frage nach Teamkontext (bezieht sich auf E "Use Nx when / Skip Nx when")                                                                                                        | N    |

### Teil 6: Libraries, Layers & Tags (Task 40)

| #   | Layout         | Titel                                | Inhalt                                                                                                                                 | Her. |
| --- | -------------- | ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ---- |
| 47  | Section header | Layering & Abstractions              | –                                                                                                                                      | E    |
| 48  | Why            | Why Layering Matters                 | Clear layers prevent circular dependencies, abstractions hide implementation                                                           | E    |
| 49  | Little What    | Typical Layers                       | UI (components) → Facade → Domain / Services → API                                                                                     | E    |
| 50  | What           | Layering: UI → Facade → Domain → API | 4 Bullets Layer-Verantwortung + Diagramm                                                                                               | E    |
| 51  | What (Bild)    | Facade Pattern in Angular            | Bild `facade-pattern-angular-architecture.png`                                                                                         | E    |
| 51a | Little What | What Is a Facade? | A facade bundles functions of several stores or services behind one API. Only what should be used from outside is exposed | N |
| 51e | Code Window Explanation | Facade Across Libraries | Beispiel: Libraries teilen Funktionen über eine Facade, auf die sich das Team geeinigt hat. Code siehe Abschnitt 9 (Teil 1: Facade, Teil 2: `index.ts` mit Public API). Rechts: Agreement, only the facade is exported, internals stay private | N |
| 51f | Code Window | Using the Facade | Consumer in anderer Library injiziert nur die Facade (Code Abschnitt 9, Teil 3) | N |
| 51b | Why | Why Use a Facade? | Bullets: one entry point for components / less coupling to internals / more comfort for developers / encapsulates functionality | N |
| 51c | Main Point | A Facade Comes at a Price | Extra layer means more maintenance. Use it only when you need it | N |
| 51d | What If? | Do We Still Need Facades? | Signal Store (Lesson 04) encapsulates and bundles state itself, so the facade loses weight. Diskussion im Plenum: where would you still add one? | N |
| 52  | How            | From Folders to Libraries            | Feature-Ordner und `data/` werden Libraries: `feature-*`, `data-access`, `ui`. Public API = `index.ts` der Library                     | N    |
| 53  | What           | Library Types                        | `feature` → `data-access` → `ui`; Richtung der Abhängigkeit nur abwärts. `ui` und `data-access` zunächst leer, gefüllt in Lesson 03/04 | N    |
| 54  | Task           | Extract Libraries                    | Task 40; letzter Schritt: bewusst `ui` aus `data-access` importieren, `nx graph` zeigt die falsche Kante                               | T    |
| 55  | What If?       | Nothing failed. Why?                 | Kein Fehler, nur der Graph zeigt es → Überleitung zu Boundaries                                                                        | N    |

### Teil 7: Building Blocks (Component Architecture, State)

Diese Blöcke füllen die `ui`- und `data-access`-Libraries und bereiten Lesson 03/04 vor.

| #   | Layout          | Titel                              | Inhalt                                                                                    | Her. |
| --- | --------------- | ---------------------------------- | ----------------------------------------------------------------------------------------- | ---- |
| 56  | Section header  | Component Architecture             | –                                                                                         | E    |
| 57  | Why             | Why Component Architecture Matters | Smart vs. dumb improves testability and reusability; clear separation makes changes safer | E    |
| 58  | Little What     | Smart vs. Dumb Components          | Smart (container), dumb (presentational), design systems use dumb                         | E    |
| 59  | How             | Smart & Dumb: Data Flow            | 4 Schritte Data-Flow + Mermaid-Diagramm + Callout (dumb = testable, reusable)             | E    |
| 59a | Main Point      | Dumb components render, smart components orchestrate           | Kernregel in einem Satz. Kein Task, aber ausführlich erklären (Beispiel aus bestehendem Google-Deck: Book-Karte in Home und Kategorieliste) | N    |
| 60  | What (Diagramm) | Component Architecture Overview    | tldraw-Diagramm                                                                           | E    |
| 61  | Section header  | State Management & Data Flow       | –                                                                                         | E    |
| 62  | Why             | Why State Management Matters       | Centralized vs. decentralized, clear data flow reduces bugs                               | E    |
| 63  | Little What     | State Management Options           | Signal Store, Redux, RxJS; centralized vs. decentralized; CQS                             | E    |
| 64  | What            | State Management Considerations    | Where does state live / unidirectional vs. two-way / CQS                                  | E    |
| 65  | What (Bild)     | Types of UI State                  | Bild `types-of-ui-state.png`                                                              | E    |

### Teil 8: Enforce Module Boundaries (Task 50)

| #   | Layout                  | Titel                       | Inhalt                                                                                                                     | Her.            |
| --- | ----------------------- | --------------------------- | -------------------------------------------------------------------------------------------------------------------------- | --------------- |
| 66  | Section header          | Module/Domain Boundaries    | –                                                                                                                          | E               |
| 67  | Why                     | Why Boundaries Matter       | Without boundaries domains import freely; refactoring risky                                                                | E               |
| 68  | What                    | Library Types and Tags      | `type` (feature, data-access, ui) und `scope` (Domäne); erlaubte Richtung. Stufe 1: nur `type`; `scope` später oder Bonus  | N               |
| 69  | Code Window Explanation | Tags in project.json        | Snippet `"tags": ["scope:book", "type:feature"]`                                                                           | N               |
| 70  | Code Window Explanation | depConstraints              | Snippet `eslint.config.mjs` mit `@nx/enforce-module-boundaries`; Erklärung: `type:feature` darf nur `data-access` und `ui` | N               |
| 71  | Why             | Options to Enforce Boundaries | Gegenüberstellung: Sheriff, eslint-plugin-boundaries, Nx (Tabelle: Konfiguration, Tag-Modell, Graph-Anbindung, Links beibehalten) | E (K1) |
| 71a | Code Window Explanation | eslint-plugin-boundaries | Regel-Snippet (`boundaries/elements`, `boundaries/element-types`); rechts: wie Elemente und erlaubte Richtungen definiert werden | E + N |
| 71b | Code Window Explanation | Nx: Same Rule | Gleiche Regel mit Tags und `depConstraints`; rechts: Zuordnung Element → Tag, `element-types` → `depConstraints` | N |
| 71c | What | Syntax Compared | Zwei Spalten Seite an Seite (eslint-plugin-boundaries vs. Nx): Fazit "very similar, Nx knows the project graph" | N |
| 72  | What (Bild)             | Enforcing Module Boundaries | Bild `enforcing-module-boundaries.png`                                                                                     | E               |
| 73  | Task                    | Enforce Module Boundaries   | Tags, `depConstraints`, den Verstoß aus Task 40 finden; ADR um Tag-Regeln ergänzen                                         | T               |
| 74  | What If?                | Limits of the Tools         | Nx prüft Projekt gegen Projekt, nicht Dateien, Zyklen, Namen. Ausblick: Architekturtest in Lesson 05                       | N               |

### Teil 9: Affected & Custom Commands (Task 60)

| #   | Layout                  | Titel                        | Inhalt                                                                                          | Her. |
| --- | ----------------------- | ---------------------------- | ----------------------------------------------------------------------------------------------- | ---- |
| 75  | Section header          | Affected & Commands          | –                                                                                               | N    |
| 76  | Why                     | Why Run Only What Changed    | Large workspaces: build/test all is slow and expensive. Cache + affected cut CI time            | N    |
| 77  | What                    | Cache and Affected           | Second run from cache (`targetDefaults`); `nx affected -t lint test build`                      | N    |
| 78  | How                     | Explaining Affected          | `--graph` shows projects, `--explain` shows why; Vergleich Änderung in `ui` vs. Feature-Library | N    |
| 79  | Code Window Explanation | A Custom Target              | `nx:run-commands` Target `check` mit `dependsOn` lint + test; `nx run-many -t check`            | N    |
| 80  | Task                    | Affected and Custom Commands | Task 60                                                                                         | T    |
| 81  | What If?                | Pipeline Bridge              | `nx affected` in CI (Lesson 08); Architekturtests (Lesson 05). Governance                       | N    |

### Teil 10: Further Topics (Performance, Build & Deploy; Bonus Runtime Configuration)

| #   | Layout          | Titel                                | Inhalt                                                                                                | Her.             |
| --- | --------------- | ------------------------------------ | ----------------------------------------------------------------------------------------------------- | ---------------- |
| 82  | Section header  | Performance Architecture             | –                                                                                                     | E                |
| 83  | Why             | Why Performance Architecture Matters | Large bundles, unnecessary change detection; strategic optimizations improve UX and cost              | E                |
| 84  | Little What     | Performance Techniques               | Lazy loading, code splitting, change detection strategy, SSR/SSG, caching                             | E                |
| 85  | What            | Performance Checklist                | Lazy load routes / OnPush / SSR-SSG / cache HTTP and computed values                                  | E                |
| 86  | What (Diagramm) | Performance Overview                 | tldraw-Diagramm                                                                                       | E                |
| 87  | Section header  | Build & Deployment Architecture      | –                                                                                                     | E                |
| 88  | Why             | Why Build & Deployment Matters       | Monorepo tooling (Nx), CI/CD, release strategies determine speed and safety                           | E                |
| 89  | Little What     | Build & Deployment Topics            | Monorepo (Nx) / CI/CD / Microfrontends (Lesson 07) / release strategies                               | E                |
| 90  | Main Point      | Promote Artifacts, Don't Rebuild     | Build once, promote through stages. Bullets: less build time, predictable deployments                 | E                |
| 91  | What (Diagramm) | Promotion Flow                       | tldraw-Diagramm dev → test → prod                                                                     | E                |
| 92  | Task (Bonus)    | Runtime Configuration                | Config aus JSON zur Laufzeit; `RuntimeConfiguration` bekommt Tag, `depConstraints` ergänzen; ADR dazu | T (E, angepasst) |
| 93  | What If?        | Same idea elsewhere                  | Federation-Manifest (Lesson 07), CD (Lesson 08)                                                       | N                |

### Teil 11: Generator (Bonus, Task 90)

| #   | Layout       | Titel                      | Inhalt                                                              | Her. |
| --- | ------------ | -------------------------- | ------------------------------------------------------------------- | ---- |
| 94  | Why          | Why Generators             | Structure by convention scales only when automated                  | N    |
| 95  | What         | Aggregate Generator        | Tabelle routing, page, client, model, schema (Valibot), metadata    | N    |
| 96  | Task (Bonus) | Generator for an Aggregate | Eigener Nx-Generator, Ergebnis mit `nx graph` und Boundaries prüfen | T    |

### Teil 12: Wrap-up

| #   | Layout          | Titel           | Inhalt                                                                                                                   | Her. |
| --- | --------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------ | ---- |
| 97  | Lessons Learned | Lessons Learned | Decide and document (ADR) / Structure by feature / Make it visible and enforce it (Nx) / Use affected and custom targets | N    |
| 98  | What If?        | What's next?    | Lesson 03 UI library, Lesson 04 data-access, Lesson 05 architecture test                                                 | N    |

---

## 5. Prüfung: nichts entfernt

Alle 68 Slidev-Folien sind mit **E**, **M** oder **T** wiederzufinden. Zusammengelegt wurden nur:

- "Typical Layers" und die Layer-Diagramm-Folie bleiben getrennt wie im Original.
- Core Building Blocks (Folie 37): 5 Bullets aus dem Original werden auf 4 gekürzt, ohne Inhalt zu verlieren (Targets + Executors zusammen).
- Mermaid-Diagramme (Cycle, Data Flow, Layering) werden in Google Slides als Bild oder Shapes nachgebaut.

## 6. Zeitbilanz

| Block       | Folien | Task-Zeit        |
| ----------- | ------ | ---------------- |
| Teile 1-3   | 26     | 10 min (Task 10) |
| Teil 4      | 7      | 10 min (Task 20) |
| Teil 5      | 13     | 15 min (Task 30) |
| Teil 6      | 9      | 15 min (Task 40) |
| Teil 7      | 10     | –                |
| Teil 8      | 9      | 15 min (Task 50) |
| Teil 9      | 7      | 15 min (Task 60) |
| Teile 10-12 | 17     | Bonus 80/90      |

Die Task-Summe von 80 min stammt aus dem Plan und enthält nur Aufgabenzeit, keine Theorie. Die Gesamtdauer der Lesson ergibt sich erst mit der Folienzeit. Kürzungskandidaten (Plan, Risiken): `scope`-Tags, Task 60. Teil 7 und Performance bleiben vollständig (K2), sind aber kompakt erklärt, ohne Task.

## 7. Nächste Schritte

1. Facade-Folien 51a bis 51d (K3) mit Lesson 04 abstimmen.
3. Bestehende Google-Slides mit `summarize_presentation` gegen dieses Outline abgleichen (Skill-Schritte 3 bis 5), dann Folien anlegen.
4. 4-MAT je Teil prüfen: Why (Folien Why), What (Little What/What), How (Task), What if (What If?). Teile 7 und 10 haben keinen How-Task.

## 8. Bestehendes Google-Deck (Stand heute)

Titel: "[workshops.de] 1000 Angular - Architecture, Structure & Mono Repositories", **22 Folien** (Folie 22 ist leer). Es ist inhaltlich das **alte, modulbasierte Deck** und deckt weder Nx noch ADR noch Boundaries ab.

| Alt-Folien | Inhalt | Umgang |
| ---------- | ------ | ------ |
| 1-2 | Titel "Architecture, Structure & Monorepositories", Section "How to structure my Angular app" | behalten als Deck-Titel (Teil 1) |
| 3, 8, 10 | "Main topics in Angular applications": Modules, Components, State Management | als Agenda/Überblick behalten (Teil 1, Folie 3-4) |
| 4-7 | Core, Shared, Feature Modules, Styling (UI Module) | **Konflikt:** Modulkonzept ist durch Standalone und Libraries überholt. Nicht löschen, als "Before: NgModules" einordnen und auf Libraries (`ui`, `feature`, `data-access`) abbilden (Teil 6) |
| 9, 11 | Components; State Management (State tree, Reducers, Selectors, Router State, Single Source of Truth) | Teil 7; Redux-Begriffe als Vergleich zum Signal Store |
| 12-13 | Component Types: Container und Presentational | = Smart/Dumb (Teil 7), zusammenführen |
| 14 | Leitfragen (structure, interaction, services in components, reuse) | Why-Folie in Teil 7 |
| 15-16 | Presentational und Container Components | Teil 7 |
| 17-18 | Beispiel Book-Karte, Diagramm | Teil 7, Beispiel für Folie 59a |
| 19 | Container vs. Presentational (nicht alles extrahieren) | Teil 7, als Callout |
| 20-21 | "Identify component types", "Set ChangeDetectionStrategy to OnPush" | Aufgaben-Reste: als Task-Folie oder Performance-Checklist (Folie 85) prüfen |
| 22 | leer | löschen oder als Platzhalter nutzen |

Vorgehen (Skill `slide-creator`, Schritte 3 bis 7): neue Folien in Englisch anlegen, bestehende anpassen, Reihenfolge per `updateSlidesPosition` herstellen. Ein vollständiger Abgleich Folie für Folie braucht noch die Notizen und Layout-Namen der Alt-Folien.


## 9. Code-Beispiel Facade (für Folien 51e und 51f)

Szenario: Die Library `feature-reading-list` braucht Funktionen aus `data-access-book` und `data-access-user`. Das Team einigt sich: Libraries teilen Funktionen nur über eine Facade. Die Facade bündelt beide Stores und bietet eine kleine API. Stores und Clients bleiben intern.

**Teil 1: Facade (`libs/reading-list/data-access/src/lib/reading-list.facade.ts`)**

```ts
@Injectable({ providedIn: 'root' })
export class ReadingListFacade {
  readonly #books = inject(BookStore);
  readonly #user = inject(UserStore);

  // Only what consumers need
  readonly books = this.#books.entities;
  readonly userName = this.#user.name;

  readonly favorites = computed(() =>
    this.#books.entities().filter((book) =>
      this.#user.favoriteIds().includes(book.id),
    ),
  );

  addFavorite(bookId: string): void {
    this.#user.addFavorite(bookId);
  }
}
```

**Teil 2: Public API (`index.ts`), nur die Facade ist exportiert**

```ts
export { ReadingListFacade } from './lib/reading-list.facade';
// BookStore and UserStore are NOT exported
```

**Teil 3: Consumer in einer anderen Library**

```ts
@Component({
  selector: 'app-favorites',
  template: `
    @for (book of facade.favorites(); track book.id) {
      <app-book-card [book]="book" />
    }
  `,
})
export class FavoritesComponent {
  protected readonly facade = inject(ReadingListFacade);
}
```

Folien-Hinweise:

- Step-Highlighting: Zeile `#books`/`#user` (Bündelung), `favorites` (abgeleitete Sicht), `index.ts` (nur Facade exportiert).
- Syntax-Highlighting über das Layout "Code Window" (Skill `slide-creator`, `references/code-window.md` vorher lesen).
- Der Code ist ein Entwurf und muss gegen die echte Bookmonkey-API (Stores aus Lesson 04) geprüft werden, bevor er auf die Folie kommt.
- Anschluss an Folie 51c/51d: Wartungsaufwand der Facade sichtbar machen (jede neue Funktion braucht eine Weiterleitung).
