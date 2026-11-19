# Lesson 04: State

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Die Teilnehmenden verstehen, wo State leben soll (global, Feature, Komponente), und bauen einen Signal Store in der `data-access`-Library.

Beworbene Inhalte: zentral vs. dezentral, Redux/CQS (nachrichtenbasiert), Signal Store.

## Voraussetzungen

Lesson 02: `data-access`-Library mit Tag `type:data-access`. Der Store wird dort angelegt und nicht später verschoben.

## Tasks

| #   | Task                       | Status                            | Min | Anmerkung                                                                       |
| --- | -------------------------- | --------------------------------- | --- | ------------------------------------------------------------------------------- |
| 00  | Zentral vs. dezentral      | neu                               | 10  | Entscheidungshilfe: wo gehört welcher State hin? Evtl. nur als Folienthema.     |
| 10  | Setup                      | existiert (`01-setup`)            | 10  |                                                                                 |
| 20  | RxJS Streams               | existiert (`02-rxjs-streams`)     | 15  |                                                                                 |
| 30  | Lifecycle Hooks            | existiert (`03-lifecycle-hooks`)  | 5   |                                                                                 |
| 40  | Computed State             | existiert (`04-computed-state`)   | 10  |                                                                                 |
| 50  | Custom Store Feature       | existiert (`05-custom-store-feature`) | 15 |                                                                                |
| 60  | Third Party Plugins        | existiert (`06-third-party-plugins`)  | 10 | hat Bonus. Kürzungskandidat.                                                    |
| 70  | Entity Management          | existiert (`07-entity-management`)    | 15 | Kürzungskandidat.                                                               |
| 80  | Events                     | existiert (`08-events`)           | 20  | deckt Redux/CQS-Gedanken ab (nachrichtenbasierte Architektur)                   |

Summe ca. 110 Minuten (bestehendes Material ca. 100, plus 10 für den neuen Task).

## Arbeit an den bestehenden Tasks

- **Mappen statt neu schreiben:** Die Tasks bleiben inhaltlich, Pfade und Projektbezug müssen auf die Nx-Struktur angepasst werden (Store liegt in `data-access`, nicht in `src/app/book`).
- Prüfen, ob `BookApiClient` und `RuntimeConfiguration` im neuen Aufbau noch dort liegen, wo die Tasks sie erwarten. Runtime Configuration ist nur noch Bonus in Lesson 02 (Task 80), der Store darf also nicht davon abhängen.
- Solution-Commits (`sample-solution`) müssen auf die neue Reihenfolge gerebased werden.

## Kürzungsoptionen

Falls der Block zu lang ist, in dieser Reihenfolge entfernen oder als Bonus markieren:
1. Third Party Plugins (hat schon Bonus-Datei)
2. Entity Management
3. Lifecycle Hooks mit Computed State zusammenlegen

## Offene Fragen

- Reicht ein Folienthema für "zentral vs. dezentral", oder braucht es einen Task?
- Wird klassisches NgRx Store (Redux) nur erwähnt oder gezeigt? Beworben ist Redux/CQS, aktuell liefert nur der Events-Task das Konzept.
- Wie sinnvoll ist die Reihenfolge der acht Tasks im neuen Projektkontext?
