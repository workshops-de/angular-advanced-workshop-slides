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

- **K1:** Die Slides "Enforcing Boundaries" und "public-api.ts + Path Aliases" nennen `eslint-plugin-boundaries`/Sheriff. Plan E1 sagt "ausschließlich Nx". Vorschlag: Folien bleiben, werden als "Other tools" gerahmt, Nx `@nx/enforce-module-boundaries` wird zur Hauptlösung ergänzt.
- **K2:** Component Architecture, State, Performance haben keinen Task und stehen nicht im 80-min-Plan. Vorschlag: bleiben als kompakte Theorieblöcke, Kürzungskandidaten bei Zeitnot (nicht löschen, ggf. in Hidden/Appendix verschieben).
- **K3:** Bei Smart/Dumb, Facade und Layering taucht "Facade" auf; Lesson 04 nutzt Signal Store. Prüfen, ob Begriff konsistent bleibt.
- **K4:** "Mono vs. Multi" und "Microfrontends" stehen in einer Zeile in "Project Structure Options". Beide bekommen echte Folien (Mono/Multi hier, Microfrontends Verweis auf Lesson 07).
- **K5:** Bilder aus Slidev (`feature-vs-layer-based.png`, `nx-dependency-graph.png`, `enforcing-module-boundaries.png`, `types-of-ui-state.png`, `facade-pattern-angular-architecture.png`) und vier tldraw-Diagramme müssen exportiert/neu gezeichnet werden.
- **K6:** `lesson.yml` von `lessons/02-architecture` enthält keine `google_slide_id`. Vor dem Anlegen in Google Slides muss die Ziel-Präsentation festgelegt werden.

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
| 71  | Little What             | Other Ways to Enforce       | Sheriff, eslint-plugin-boundaries (Links beibehalten)                                                                      | E (Konflikt K1) |
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

Task-Summe 80 min. Kürzungskandidaten (Plan, Risiken): `scope`-Tags, Task 60. Zusätzlich aus Folienzeit: Teil 7 (Component/State) und Performance (K2).

## 7. Nächste Schritte

1. Ziel-Präsentation in `lesson.yml` (`google_slide_id`) festlegen (K6).
2. K1 bis K3 entscheiden (Rahmung Sheriff/eslint-plugin-boundaries, Teil 7 behalten oder in Appendix).
3. Bestehende Google-Slides mit `summarize_presentation` gegen dieses Outline abgleichen (Skill-Schritte 3 bis 5), dann Folien anlegen.
4. 4-MAT je Teil prüfen: Why (Folien Why), What (Little What/What), How (Task), What if (What If?). Teile 7 und 10 haben keinen How-Task.
