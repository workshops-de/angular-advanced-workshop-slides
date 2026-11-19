# Lesson 03: Component Libraries

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Die Teilnehmenden bauen auf der `ui`-Library aus Lesson 02 eine kleine, dokumentierte Component Library. Sie nutzen CDK und Angular Aria statt Eigenbau und lernen, Drittanbieter-Libraries zu bewerten.

Beworbene Inhalte: Angular CDK, Wiederverwenden statt neu erfinden, Drittanbieter evaluieren, UI/UX-Pitfalls, eigene dokumentierte Library.

## Voraussetzungen

Lesson 02: Nx-Workspace mit Libraries und Tags. Insbesondere der Tag `type:ui` und die Regel, dass `ui` nichts aus `data-access` importiert.

## Tasks

| #   | Task                     | Status | Min | Lernziel                                                                                |
| --- | ------------------------ | ------ | --- | --------------------------------------------------------------------------------------- |
| 10  | UI-Library               | neu    | 10  | Eigene Komponenten in die `ui`-Library, öffentliche API über `index.ts`                 |
| 20  | Angular CDK              | neu    | 20  | Ein Baustein per CDK lösen (Kandidat: Overlay oder Dialog), statt ihn selbst zu bauen   |
| 30  | Angular Aria + Styling   | neu    | 20  | Headless Verhalten nutzen, eigenes Styling darüberlegen. **Bonus-Kandidat.**            |
| 40  | Storybook                | neu    | 20  | Setup per Generator, eine Story als Dokumentation                                       |
| 50  | Browser-Tests            | neu    | 15  | Komponenten im echten Browser testen (an die Stories gekoppelt, wenn möglich)           |
| 60  | Drittanbieter bewerten   | neu    | 10  | Bewertungskriterien (Wartung, Barrierefreiheit, Bundle-Größe, Theming), kurze Evaluation |
| 70  | Enterprise UX Patterns   | neu    | 15  | Auswahl offen                                                                           |

## Hinweise zu den Tasks

- **Storybook und Browser-Tests teilen das Setup.** Beide brauchen einen echten Browser. Wenn der Generator-Setup beides abdeckt, zahlt das Setup doppelt ein. Prüfen im Spike.
- **UI/UX-Pitfalls** sind beworben, aber noch keinem Task zugeordnet. Vorschlag: In Task 20 und 30 als Leitfragen einbauen (Fokusführung, Tastatur, ARIA), statt einen eigenen Task zu schreiben.
- **Enterprise UX Patterns:** Kandidaten sind Tabelle mit Filter/Sort/Paging, Dirty-Guard im Formular, Master-Detail, Loading- und Error-States, Toast-Feedback. Auswahl und Umfang offen.
- **Boundaries nutzen:** Die UI-Library hat keine Abhängigkeit auf `data-access`. Ein bewusster Verstoß wäre ein gutes Lehrbeispiel.

## Offene Fragen

- Welche CDK-Funktion eignet sich am besten für einen 20-Minuten-Task im Bookmonkey-Kontext?
- Wie reif ist Angular Aria aktuell, und passt es in ein 20-Minuten-Zeitfenster?
- Storybook für Angular in Nx: Generatoren noch nicht recherchiert.
- Welche Drittanbieter-Library nehmen wir als Evaluationsbeispiel?
- Welche Enterprise UX Patterns wollen wir wirklich?

## Risiken

- Storybook-Setup ist der größte Einzelposten. Ohne vorgewärmte Umgebung und Checkpoint kann er den Block sprengen.
- Zu viele Teilthemen für 110 Minuten. Bonus und Kürzung (Aria, UX Patterns) vorab festlegen.
