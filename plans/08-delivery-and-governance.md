# Lesson 08: Delivery & Governance

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Das Projekt bekommt eine Pipeline, die nur das Betroffene baut und testet. Die Teilnehmenden lernen Deployment-Konzepte und finden Architektur-Anti-Patterns im eigenen Projekt.

Beworbene Inhalte: Codeanalyse, Continuous Integration, Continuous Deployment, architekturelle Anti-Patterns, Architekturarbeit im Team.

## Voraussetzungen

Lesson 02 (Affected und eigene Commands), Lesson 05 (Tests und Architekturtests), Lesson 06 (Runtime Configuration).

## Tasks

| #   | Task                        | Status | Min | Lernziel                                                                    |
| --- | --------------------------- | ------ | --- | --------------------------------------------------------------------------- |
| 10  | Continuous Integration      | neu    | 15  | Pipeline mit `nx affected -t lint test build`                               |
| 20  | Codeanalyse                 | neu    | 10  | Statische Analyse im Workflow: Lint, Architekturtests, ggf. `nx graph`      |
| 30  | Continuous Deployment       | neu    | 10  | Ein Build, mehrere Stages (Brücke zu Runtime Configuration). Eher Skizze.   |
| 40  | Anti-Patterns finden        | neu    | 15  | Verstöße im Projekt erkennen und beheben                                    |

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
