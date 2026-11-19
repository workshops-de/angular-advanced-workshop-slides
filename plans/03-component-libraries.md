# Lesson 03: Component Libraries

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Die Teilnehmenden verstehen, warum fertige UI-Libraries in Enterprise-Projekten selten reichen: Man zieht sie rein, braucht aber für die eigenen Anforderungen eine **eigene Abstraktion**, die Bausteine der Library zu Compositions kombiniert. Dafür bauen sie in der bereits vorhandenen `ui`-Library eine generische Komponente: **Autocomplete zur Auswahl einer beliebigen Entity** (im Bookmonkey ein Buch oder ein Autor).

Beworbene Inhalte: Angular CDK, Wiederverwenden statt neu erfinden, Drittanbieter evaluieren, UI/UX-Pitfalls, eigene dokumentierte Library.

## Voraussetzungen

Lesson 02: Nx-Workspace mit **bereits angelegten, zunächst leeren Libraries** (Entscheidung E7, siehe unten):

| Library          | Tags (Vorschlag)                      | Rolle in dieser Lesson                              |
| ---------------- | ------------------------------------- | --------------------------------------------------- |
| `ui`             | `type:ui`                             | Hier entsteht die Autocomplete-Komponente.          |
| `data-access`    | `type:data-access`, `scope:book`      | Liefert die Daten (Bücher, Autoren), wird erst in Lesson 04 gefüllt. |
| `feature-*`      | `type:feature`, `scope:book`          | Setzt `ui` und `data-access` zusammen.              |

Die Regel `ui` importiert nichts aus `data-access` gilt bereits (Lesson 02, Task 50).

## Dramaturgie

Vergleichen → Bauen → Abstrahieren → Einsetzen → Dokumentieren → Absichern:

1. **Vergleichen (Folien, kein Task):** Welche Library für welchen Zweck? Stärken und Schwächen nebeneinander.
2. **Bauen:** Autocomplete mit Angular Material bzw. CDK für ein festes Beispiel (Buch).
3. **Abstrahieren:** Generisch machen, sodass dieselbe Komponente Bücher und Autoren auswählt.
4. **Einsetzen:** In der Feature-Library zusammenstecken. Hier greifen die Boundaries: `ui` kennt keine Daten, die Feature-Library reicht sie hinein.
5. **Dokumentieren:** Storybook.
6. **Absichern:** Browser-Tests.

Der rote Faden: **Library einziehen → eigene Abstraktion darüberlegen → in der eigenen UI-Library bereitstellen.**

## Tasks

| #   | Task                              | Status | Min | Lernziel                                                                                         |
| --- | --------------------------------- | ------ | --- | ------------------------------------------------------------------------------------------------ |
| 10  | Autocomplete mit Material/CDK     | neu    | 20  | Ein Baustein aus Library-Komponenten (`MatAutocomplete`, `MatFormField`, Overlay über CDK) für ein festes Beispiel |
| 20  | Generische Entity-Auswahl         | neu    | 20  | Abstraktion: Komponente kennt die Entity nicht, nur Suchfunktion, Anzeige und Schlüssel          |
| 30  | Einsatz in Feature und Boundaries | neu    | 15  | Book und Author über dieselbe Komponente. Daten kommen aus `data-access`, `ui` bleibt frei davon |
| 40  | Storybook                         | neu    | 20  | Setup per Generator, eine Story als Dokumentation (mit Fake-Suchfunktion)                        |
| 50  | Browser-Tests                     | neu    | 15  | Komponente im echten Browser testen (an die Stories gekoppelt, wenn möglich)                     |
| 60  | Bonus: Angular Aria               | neu, **Bonus** | 20 | Dieselbe Auswahl headless mit Angular Aria (Combobox/Listbox) und eigenem Styling              |
| 70  | Bonus: Enterprise UX Patterns     | neu, **Bonus** | 15 | Auswahl offen                                                                                  |

Summe ohne Bonus: **90 min**. Dazu kommt Folienzeit für den Library-Vergleich (ca. 20 min), zusammen im Rahmen von 110 min.

Der frühere Task "UI-Library anlegen" **entfällt**, weil die Libraries in Lesson 02 entstehen. Der frühere Task "Drittanbieter bewerten" wird reiner Folieninhalt (siehe unten).

### Task 10: Autocomplete mit Material/CDK

Wir nehmen **nicht** Spartan NG (zu komplex für das Zeitfenster), sondern Angular Material, das auf dem CDK aufbaut.

- Ausgangspunkt: Eingabefeld mit Vorschlagsliste für Bücher, direkt gegen eine feste Liste im Task.
- Bausteine: `MatFormField`, `MatInput`, `MatAutocomplete`. Das Overlay (Positionierung, Schließen, Fokus) kommt aus dem CDK und wird nicht selbst gebaut.
- Leitfragen für UI/UX-Pitfalls: Tastaturbedienung (Pfeiltasten, Enter, Escape), Fokusführung, Screenreader (ARIA-Rollen kommen von der Library), Verhalten bei leerem Ergebnis, Debounce beim Tippen.
- Offen: Reicht Material allein, oder zeigen wir bewusst einen CDK-Baustein (`cdk/overlay`, `cdk/listbox`), um die Beziehung Material → CDK sichtbar zu machen?

### Task 20: Generische Entity-Auswahl

Kern der Lesson. Die Komponente soll für beliebige Entities funktionieren.

Ausgangsidee für die öffentliche API (zu validieren im Spike):

| Baustein         | Idee                                                                                   |
| ---------------- | -------------------------------------------------------------------------------------- |
| Typparameter     | `EntitySelect<T>`, generische Standalone-Komponente                                    |
| Suche            | `search = input.required<(term: string) => Observable<T[]>>()` oder Signal/`resource`-basiert |
| Anzeige          | `displayWith = input.required<(entity: T) => string>()`                                |
| Schlüssel        | `trackBy`/`key` für die Liste                                                          |
| Optionen-Template | Optionales `ng-template` per Content Projection für eigene Darstellung (Cover, Autorenname) |
| Wert             | `model<T \| null>()` oder Anbindung an Formulare (ControlValueAccessor bzw. Signal Forms, abhängig von der Angular-Version) |

- Lernziel: Die Komponente enthält **keine Fachlichkeit**. Sie bekommt Verhalten und Darstellung hineingereicht.
- Offen: Formularanbindung ja oder nein? Ein `model()` ist einfacher, ein ControlValueAccessor realistischer. Entscheidung im Spike.

### Task 30: Einsatz in Feature und Boundaries

- Die Feature-Library setzt `EntitySelect` zweimal ein: für Bücher und für Autoren. Die Suchfunktion kommt aus `data-access`.
- Die Abhängigkeitsrichtung ist die Pointe: `feature` kennt `ui` und `data-access`, `ui` kennt keines von beiden.
- **Bewusster Verstoß als Lehrbeispiel:** Die Komponente ruft direkt `BookApiClient` auf. `nx lint` schlägt an (Regel aus Lesson 02). Die Lösung ist die Suchfunktion als Input.
- Falls `data-access` in Lesson 04 erst gefüllt wird: Die Suchfunktion liegt hier als Platzhalter im Feature, der Store (Lesson 04) ersetzt sie.

### Task 40 und 50: Storybook und Browser-Tests

- Eine Story für die Komponente mit Fake-Suchfunktion. Das zeigt zusätzlich, dass die Abstraktion ohne Backend funktioniert.
- Browser-Tests laufen gegen die Story (Interaktion: Tippen, Option wählen, Escape).
- Storybook und Browser-Tests teilen das Setup. Wenn der Generator beides abdeckt, zahlt das Setup doppelt ein. Prüfen im Spike.

### Task 60 (Bonus): Angular Aria

Dieselbe Auswahl mit Angular Aria (headless, nur Verhalten und Barrierefreiheit) und eigenem Styling. Gegenüberstellung zu Task 10: Material liefert Optik und Verhalten, Aria nur Verhalten.

## Folieninhalte ohne Task

Der Library-Vergleich steht **vor Task 10** und ist eine Gegenüberstellung von Stärken und Schwächen. Keine Empfehlung, keine Aufgabe.

| Library          | Charakter (🔎 Entwurf aus Vorwissen, Web-Recherche nötig)                                    |
| ---------------- | -------------------------------------------------------------------------------------------- |
| Angular Material | Offizielle Library, Material Design, gute Barrierefreiheit, Theming über Design Tokens. Eigenes Design weicht schnell vom Standard ab. |
| Angular CDK      | Verhalten ohne Optik (Overlay, Listbox, Drag & Drop, A11y). Maximale Freiheit, mehr Eigenleistung. |
| Angular Aria     | Neuer, headless Ansatz des Angular-Teams für zugängliche Muster. Reifegrad und Umfang prüfen. |
| PrimeNG          | Sehr großer Komponentenumfang (Tabellen, Charts), mehrere Themes. Schneller Start, dafür Bundle-Größe und Anpassungstiefe beachten. |
| DevExpress       | Kommerziell, sehr mächtige Daten- und Grid-Komponenten, Support. Lizenzkosten, Lock-in. |
| Spartan NG       | shadcn-Ansatz mit Tailwind, Code gehört dem Projekt. Hoher Aufwand, deshalb in dieser Lesson nicht im Task. |

Vergleichsdimensionen: Wartung und Community, Barrierefreiheit, Bundle-Größe, Theming und Anpassbarkeit, Komponentenumfang, Lizenz und Kosten, Lock-in.

Die Überleitung zum Task: **Keine Library passt ohne Abstraktion.** Deshalb bekommt jedes Team eine eigene UI-Library, die Bausteine kombiniert und Entscheidungen (z. B. Standardverhalten, Design) an einer Stelle bündelt.

Offen: Folien liegen in Google Slides und werden über den Skill `slide-creator` abgesichert.

## Hinweise

- **UI/UX-Pitfalls** (beworben) stecken als Leitfragen in Task 10: Fokusführung, Tastatur, ARIA.
- **Enterprise UX Patterns** sind Bonus: Tabelle mit Filter/Sort/Paging, Dirty-Guard im Formular, Master-Detail, Loading- und Error-States, Toast-Feedback.
- **Mehrere Libraries im Enterprise-Kontext:** Die Folien sollen zeigen, dass in einer großen Struktur mit mehreren Teams neben `ui` weitere Library-Typen existieren (`data-access`, `feature`, `util`). Die Struktur dafür entsteht in Lesson 02. Hier wird sie nur genutzt.

## Recherche offen (🔎 Web)

Diese Punkte sind noch nicht recherchiert. Die Webrecherche folgt, Ergebnisse kommen in Abschnitt 9 des Master-Plans.

- 🔎 **Library-Vergleich (Folien):** Aktueller Stand von Material, CDK, Angular Aria, PrimeNG, DevExpress, Spartan NG: Wartung, Barrierefreiheit, Bundle-Größe, Theming, Lizenz, Angular-Version-Support. Die Tabelle oben ist Entwurf aus Vorwissen.
- 🔎 **Angular Aria:** Reifegrad, Umfang (Combobox, Listbox usw.), API-Stabilität, Eignung für Task 60.
- 🔎 **Material/CDK Autocomplete:** Aktuelle API (`MatAutocomplete`, ggf. CDK-Alternativen wie `cdk/listbox`, `cdk/combobox`), Verhalten mit Signals.
- 🔎 **Formularanbindung:** Signal Forms vs. ControlValueAccessor in der Angular-Version des Workshop-Repos.
- 🔎 **Storybook für Angular in Nx:** Generatoren, Setup, Kopplung mit Browser-Tests.
- 🔎 **Browser-Tests:** Vitest Browser Mode bzw. Playwright gegen Stories, Zusammenspiel mit Nx.
- 🔎 **Angular-Version und Material-Version** des Workshop-Repos (bestimmt API-Namen).

## Offene Fragen

- Reicht Material, oder zeigen wir einen reinen CDK-Baustein in Task 10?
- Formularanbindung der generischen Komponente: `model()`, ControlValueAccessor oder Signal Forms?
- Suche als Funktions-Input oder `resource`-basiert?
- Storybook für Angular in Nx: Generatoren noch nicht recherchiert.
- Wie reif ist Angular Aria aktuell? Reicht es für Task 60?
- Wann liefert Lesson 04 die Daten? Platzhalter-Suche in Task 30 nötig?

## Risiken

- Storybook-Setup ist der größte Einzelposten. Ohne vorgewärmte Umgebung und Checkpoint kann er den Block sprengen.
- Die generische Abstraktion (Task 20) kann zu viel auf einmal werden (Generics, Content Projection, Formularanbindung). Im Zweifel Formularanbindung streichen.
- Lesson 02 muss die drei Libraries wirklich leer anlegen und taggen, sonst fehlt hier die Grundlage.
