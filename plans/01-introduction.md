# Lesson 01: Introduction

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Alle Teilnehmenden haben eine lauffähige Umgebung und wissen, wie der Workshop abläuft. Der Fokus der Gruppe wird abgeholt und die Themenreihenfolge wird bei Bedarf angepasst.

## Voraussetzungen

Keine.

## Tasks

| #   | Task                           | Status       | Min | Anmerkung                                                                    |
| --- | ------------------------------ | ------------ | --- | ---------------------------------------------------------------------------- |
| 00  | Preparation                    | existiert    | 10  | Clone `angular-advanced-workshop`, `npm install`, `npm start`, `bookmonkey-api` |
| 20  | Choose your lessons and focus  | existiert    | 15  | Trainer passt Themen an. Ausgangspunkt ist die Lesson-Liste im MASTER-PLAN.  |

## Anpassungen gegenüber heute

- **Preparation erweitern** um alles, was später Zeit frisst: Playwright-Browser (`npx playwright install`, für Browser-Tests und E2E), Nx-Cache vorwärmen, Node-Version prüfen.
- **Netzwerk:** Prüfen, ob Installationen in der Schulungsumgebung blockiert sind (npm-Registry, Browser-Download).
- **Orientierung:** Eine Übersicht des durchgehenden Projekts (Bookmonkey wächst von der App zum Mono-Repo mit Micro-Frontend) gehört in die Folien, nicht in einen Task.

## Offene Fragen

- Welche Node- und Angular-Version setzt das Repo voraus?
- Soll "Choose your lessons" die neue Lesson-Reihenfolge widerspiegeln? Der Task ist aktuell generisch.

## Risiken

- Installationsprobleme am ersten Tag kosten Zeit, die später fehlt. Deshalb sollte die Preparation vorab per E-Mail an die Teilnehmenden gehen.
