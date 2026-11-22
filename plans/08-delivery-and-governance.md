# Lesson 08: Delivery & Governance

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Die Teilnehmenden lernen zuerst, nur das Betroffene auszuführen (Affected und eigene Commands, aus Lesson 02 hierher verlegt). Das Projekt bekommt dann eine Pipeline, die nur das Betroffene baut und testet. Die Teilnehmenden lernen Deployment-Konzepte und finden Architektur-Anti-Patterns im eigenen Projekt.

Beworbene Inhalte: Codeanalyse, Continuous Integration, Continuous Deployment, architekturelle Anti-Patterns, Architekturarbeit im Team.

## Voraussetzungen

Lesson 02 (Libraries, Tags und Boundaries als Grundlage für `affected`, Runtime Configuration nur als Bonus, CD bleibt deshalb Skizze), Lesson 05 (Tests und Architekturtests).

## Tasks

| #   | Task                        | Status | Min | Lernziel                                                                    |
| --- | --------------------------- | ------ | --- | --------------------------------------------------------------------------- |
| 10  | Affected und eigene Commands | neu (aus Lesson 02, Task 60) | 15  | Cache, `nx affected` (`--graph`, `--explain`), eigenes Target mit `nx:run-commands`, `dependsOn`, `nx run-many` |
| 20  | Continuous Integration      | neu    | 15  | Pipeline mit `nx affected -t lint test build`                               |
| 30  | Codeanalyse                 | neu    | 10  | Statische Analyse im Workflow: Lint, Architekturtests, ggf. `nx graph`      |
| 40  | Continuous Deployment       | neu    | 10  | Ein Build, mehrere Stages (Brücke zu Runtime Configuration). Eher Skizze.   |
| 50  | Anti-Patterns finden        | neu    | 15  | Verstöße im Projekt erkennen und beheben                                    |

Summe: **65 min**.

## Task 10 im Detail

Idee: Zuerst eine Änderung in `ui` machen und mit `nx affected -t test --graph` sehen, welche Projekte betroffen sind. Danach ein eigenes Target anlegen, das die Struktur nutzt.

- Cache: zweiter Lauf von `build` oder `test` kommt aus dem Cache (`targetDefaults` mit `"cache": true`).
- `nx affected -t lint test build`: Wirkung einer Änderung in `ui` gegenüber einer Änderung in einer Feature-Library.
- `--explain` zeigt, warum ein Projekt betroffen ist.
- Eigener Command als Target in `project.json` oder `nx.json`, z. B. `nx:run-commands` für einen Check oder ein Skript. Kandidat: ein Target `check`, das über `dependsOn` Lint und Test bündelt.
- `nx run-many -t check` über alle Projekte, danach dasselbe nur affected.
- Offen: Konkretes Beispiel für den eigenen Command festlegen. Er sollte später in der CI (Task 20) und beim Architekturtest (Lesson 05) wiederverwendbar sein.

## Hinweise

- **CI:** Aus der Nx-Doku: GitHub Actions mit `nrwl/nx-set-shas`, `fetch-depth: 0` und `nx affected -t lint test build`. Noch nicht ausprobiert.
- **Codeanalyse** kann auf die Architekturtests aus Lesson 05 aufsetzen. Das verbindet Testing und Delivery.
- **CD** ist am schwersten praktisch zu machen. Realistisch ist eine Diskussion mit Skizze, kein echtes Deployment.
- **Anti-Patterns** sollen aus dem Projekt selbst kommen. Wir bauen vorab (oder im Task) kontrolliert Verstöße ein, z. B. zirkuläre Abhängigkeit, `HttpClient` in einer Komponente, Feature-Abhängigkeiten quer, God-Component. Die Architekturtests aus Lesson 05 finden einen Teil davon automatisch.

## Offene Fragen

- Welche CI-Plattform? GitHub Actions ist in der Nx-Doku beschrieben. Die Teilnehmenden haben vermutlich andere Systeme.
- Läuft die Pipeline wirklich im Workshop oder wird sie nur lokal simuliert (`nx affected` lokal)?
- Wie viel CD ist realistisch? Nur Konzept, oder ein Build-Artefakt pro Stage?
- Anti-Patterns: welche Liste? Vorschlag: aus den realen Fehlern der bisherigen Aufgaben ableiten.
- Architekturarbeit im Team (beworbener Punkt): Wo wird sie sichtbar? Vorschlag: Abschlussdiskussion mit ADRs und Architekturtests als Teamvereinbarung.
