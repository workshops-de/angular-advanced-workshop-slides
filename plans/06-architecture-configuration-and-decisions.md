# Lesson 06: Architecture: Configuration & Decisions

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Die App wird **zur Laufzeit** konfigurierbar (ein Build, viele Stages). Die Entscheidung dafür wird als Architecture Decision Record festgehalten. Beides bereitet das Micro-Frontend vor.

Beworbene Inhalte: Architektur-Trade-off-Analyse, Architekturarbeit im Team.

## Voraussetzungen

Lessons 02 bis 05. Der Store (Lesson 04) darf nicht von der Runtime Configuration abhängen. Das ist wichtig, weil `BookApiClient` den Endpoint künftig aus der Konfiguration holt.

## Tasks

| #   | Task                    | Status                                  | Min | Anmerkung                                                                  |
| --- | ----------------------- | --------------------------------------- | --- | -------------------------------------------------------------------------- |
| 10  | Runtime Configuration   | existiert (`04-runtime-configuration`)  | 10  | **entkoppeln**: letzter Schritt "Update ESLint boundaries" entfällt oder wird ein Nx-Tag-Schritt |
| 20  | Architecture Decision Record | existiert (`05-architecture-decision-record`) | 10 | dokumentiert die Runtime-Configuration-Entscheidung (Optionen, Konsequenzen) |

## Arbeit an den bestehenden Tasks

- **Runtime Configuration:** Der Schritt "Update ESLint boundaries" bezieht sich auf die entfernte `eslint-plugin-boundaries`-Lösung (Entscheidung E1). Ersatz: Falls die Konfiguration in einer eigenen Library liegt, braucht sie einen passenden Tag.
- **Pfade:** `/assets/configuration/api-endpoints.json` und die Platzierung des Service müssen zur Nx-Struktur passen. Offen ist, in welcher Library `RuntimeConfiguration` liegt.
- **Brücke zum Micro-Frontend:** Der Task soll ausdrücklich zeigen, dass dieselbe Idee (Konfiguration zur Laufzeit statt Build-Zeit) später beim Federation-Manifest wieder auftaucht.
- **ADR:** Ein sinnvoller Zusatz wäre, einen zweiten, kleinen ADR selbst zu schreiben (z. B. "Warum Nx?"). Das ist ein Bonus-Kandidat und stärkt die Trade-off-Analyse.

## Offene Fragen

- Lesson ist mit 20 Minuten sehr klein. Mit Lesson 02 oder 07 zusammenlegen, oder eigenständig lassen? (Siehe MASTER-PLAN, Frage 7.)
- In welcher Library liegt die Runtime-Konfiguration?
- Soll ein Trade-off-Task hinzukommen (z. B. Optionsabwägung in Teamarbeit), oder reicht der ADR?
