# Lesson 03: Component Libraries

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Die Teilnehmenden verstehen, warum fertige UI-Libraries in Enterprise-Projekten selten reichen: Man zieht sie rein, braucht aber für die eigenen Anforderungen eine **eigene Abstraktion**, die Bausteine der Library zu Compositions kombiniert. Dafür bauen sie in der bereits vorhandenen `ui`-Library eine generische Komponente: **Autocomplete zur Auswahl einer beliebigen Entity** (im Bookmonkey ein Buch oder ein Autor).

Beworbene Inhalte: Angular CDK, Wiederverwenden statt neu erfinden, Drittanbieter evaluieren, UI/UX-Pitfalls, eigene dokumentierte Library.

## Voraussetzungen

Lesson 02: Nx-Workspace mit **bereits angelegten, zunächst leeren Libraries** (Entscheidung E10):

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
| 25  | Host-Directive: Komposition       | neu    | 15  | Verhalten in kleine Direktiven auslagern und per `hostDirectives` in die Komponente komponieren (erste eigene UI-Abstraktion) |
| 30  | Einsatz in Feature und Boundaries | neu    | 15  | Book und Author über dieselbe Komponente. Daten kommen aus `data-access`, `ui` bleibt frei davon |
| 40  | Storybook                         | neu    | 20  | Setup per Generator, eine Story als Dokumentation (mit Fake-Suchfunktion)                        |
| 50  | Browser-Tests                     | neu    | 15  | Komponente im echten Browser testen (an die Stories gekoppelt, wenn möglich)                     |
| 60  | Bonus: Angular Aria               | neu, **Bonus** | 20 | Dieselbe Auswahl headless mit Angular Aria (`ngCombobox` + `ngListbox`) und eigenem Styling. Aria ist ein echtes Autocomplete-Muster, daher auch als Alternative zu Task 10 denkbar. |
| 70  | Bonus: Routed Dialog              | neu, **Bonus** | 20 | Dialog über eigenen Router-Outlet öffnen statt über Service. Aufgabe optional, Musterlösung zeigt die Umsetzung. |

Summe ohne Bonus: **105 min** (mit Folien eng, siehe Risiken). Dazu kommt Folienzeit für den Library-Vergleich (ca. 20 min), zusammen im Rahmen von 110 min.

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
- **Entscheidung (E8):** Formularanbindung per **Signal Forms** (Workshop-Repo nutzt Angular 22, dort laut Skill stabil und Empfehlung für neue Formulare). Kein ControlValueAccessor. Konkrete API für ein Custom Control (🔎) im Spike klären.
- Host-Bindings (`role`, `aria-*`) über die `host`-Property, nicht über `@HostBinding`/`@HostListener`.
- Suche bevorzugt `resource`-basiert statt `Observable`-Input (🔎 prüfen).

### Task 25: Host-Directive, Komposition statt Vererbung

Erste eigene UI-Abstraktion: Die Komponente kombiniert Verhalten aus kleinen Direktiven, statt alles selbst zu verdrahten oder zu erben.

Ausgangsidee (im Spike validieren, 🔎 Web-Recherche zu `hostDirectives`):

| Direktive         | Aufgabe                                                                 |
| ----------------- | ----------------------------------------------------------------------- |
| `DebouncedInput`  | Wartet nach dem Tippen und liefert den Suchbegriff als Output (`termChange`) |
| `EntityOption`    | Darstellung einer Option (`displayWith`, Template)                       |
| optional: Fokus/ARIA | z. B. Fokus beim Öffnen, zusätzliche `aria-*`-Attribute                |

- Die Komponente bindet sie über `hostDirectives: [{ directive: DebouncedInput, inputs: [...], outputs: [...] }]` ein und reicht nur gezielt Inputs und Outputs nach außen.
- Lernziel: Verhalten ist einzeln testbar und wiederverwendbar. Die Komponente wird dünn. Gegenüberstellung zu Vererbung und zu Kopieren.
- Stolpersteine für die Folien: Host-Directives müssen standalone sein, nur explizit weitergereichte Inputs und Outputs sind sichtbar, Reihenfolge der Host-Directive-Ausführung.
- Offen: Welche Direktiven sind wirklich sinnvoll, ohne künstlich zu wirken? Kandidat `DebouncedInput` ist der einfachste.

### Task 30: Einsatz in Feature und Boundaries

- Die Feature-Library setzt `EntitySelect` zweimal ein: für Bücher und für Autoren. Die Suchfunktion kommt aus `data-access`.
- Die Abhängigkeitsrichtung ist die Pointe: `feature` kennt `ui` und `data-access`, `ui` kennt keines von beiden.
- **Bewusster Verstoß als Lehrbeispiel:** Die Komponente ruft direkt `BookApiClient` auf. `nx lint` schlägt an (Regel aus Lesson 02). Die Lösung ist die Suchfunktion als Input.
- Falls `data-access` in Lesson 04 erst gefüllt wird: Die Suchfunktion liegt hier als Platzhalter im Feature, der Store (Lesson 04) ersetzt sie.

### Task 40 und 50: Storybook und Browser-Tests

- Eine Story für die Komponente mit Fake-Suchfunktion. Das zeigt zusätzlich, dass die Abstraktion ohne Backend funktioniert.
- Browser-Tests laufen gegen die Story (Interaktion: Tippen, Option wählen, Escape).
- Storybook und Browser-Tests teilen das Setup. Wenn der Generator beides abdeckt, zahlt das Setup doppelt ein. Prüfen im Spike.

### Task 70 (Bonus): Routed Dialog

Dialoge werden über Routing geöffnet, nicht über einen Service, der die aufrufende Komponente an den Dialog koppelt.

- Idee: Ein eigener, benannter `router-outlet` (z. B. `outlet: 'dialog'`) nimmt unabhängige Dialoge auf. Der Dialog wird per `routerLink` oder `router.navigate([{ outlets: { dialog: [...] } }])` geöffnet und über Navigation geschlossen.
- Vorteile (für die Folien): Dialog ist per URL adressierbar (Deep Link, Back-Button schließt ihn), keine Kopplung zwischen aufrufender Komponente und Dialog, Daten kommen über Route-Parameter und Resolver.
- Nachteile: Ergebnis zurück an den Aufrufer ist umständlicher (Router-State, Service oder Query-Parameter), Dialog-Lifecycle hängt am Routing.
- Anbindung an den Autocomplete: der Dialog-Button aus dem Ausblick (siehe unten) öffnet über diesen Weg den Auswahldialog.
- Format: reine Bonusaufgabe für Interessierte, die Musterlösung zeigt die komplette Umsetzung. Material-Dialog oder CDK Dialog als Container (🔎 prüfen).

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

### Ausblick: Entity-Auswahl als Enterprise-Pattern (nur Slides)

Der Autocomplete aus den Tasks ist die Grundstufe. Als Ausblick zeigen die Folien, wie weit dasselbe generische Prinzip trägt. Jede Stufe können Teilnehmende später selbst bauen. Keine Aufgabe.

| Stufe | Erweiterung                                                                                         |
| ----- | --------------------------------------------------------------------------------------------------- |
| 1     | Autocomplete (Tasks 10 bis 30)                                                                      |
| 2     | Zusätzlicher Button neben dem Feld öffnet einen **Auswahldialog** mit echter Suche gegen die Datenbank (Filter, Paging) |
| 3     | Im Dialog kann zusätzlich eine **neue Entity angelegt** (oder bearbeitet) werden                    |
| 4     | Das Ganze als ein generisches Eingabefeld für jede Entity: **suchen, anlegen, aktualisieren**       |

Dazu das Pattern **Routed Dialogs** (Konzept auf den Folien, Umsetzung als Bonus-Task 70).

Weitere Enterprise-UX-Pattern (Tabelle mit Filter/Sort/Paging, Dirty-Guard im Formular, Master-Detail, Loading-/Error-States, Toast-Feedback) sind nicht mehr Teil dieses Plans und können als Folienausblick genannt werden.

## Hinweise

- **UI/UX-Pitfalls** (beworben) stecken als Leitfragen in Task 10: Fokusführung, Tastatur, ARIA.
- **Enterprise UX Patterns:** nur Ausblick auf den Folien (siehe oben), Routed Dialog als Bonus-Task 70.
- **Mehrere Libraries im Enterprise-Kontext:** Die Folien sollen zeigen, dass in einer großen Struktur mit mehreren Teams neben `ui` weitere Library-Typen existieren (`data-access`, `feature`, `util`). Die Struktur dafür entsteht in Lesson 02. Hier wird sie nur genutzt.

## Vorinstallierte Pakete (Entscheidung E9)

Im Workshop-Repo (`angular-workshop`) sind aktuell nur `@angular/cdk` (22.1) und `@angular/forms` (22.2) in der `package.json`. Folgende Pakete müssen **vorinstalliert** werden, damit keine Aufgabenzeit verloren geht (analog zu E6 in Plan 02):

- `@angular/material` (passend zur Angular-Version)
- `@angular/aria` (für Task 60 bzw. Alternative zu Task 10)
- Storybook- und Browser-Test-Pakete (nach Recherche)

## Recherche offen (🔎 Web)

Diese Punkte sind noch nicht recherchiert. Die Webrecherche folgt, Ergebnisse kommen in Abschnitt 9 des Master-Plans.

- 🔎 **Library-Vergleich (Folien):** Aktueller Stand von Material, CDK, Angular Aria, PrimeNG, DevExpress, Spartan NG: Wartung, Barrierefreiheit, Bundle-Größe, Theming, Lizenz, Angular-Version-Support. Die Tabelle oben ist Entwurf aus Vorwissen.
- 🔎 **Angular Aria:** Reifegrad, Umfang (Combobox, Listbox usw.), API-Stabilität, Eignung für Task 60.
- 🔎 **Material/CDK Autocomplete:** Aktuelle API (`MatAutocomplete`, ggf. CDK-Alternativen wie `cdk/listbox`, `cdk/combobox`), Verhalten mit Signals.
- 🔎 **Signal Forms Custom Control:** Schnittstelle für eigene Controls in Angular 22 (Entscheidung E8 steht, API offen).
- 🔎 **`hostDirectives`:** Aktuelle Syntax, Verhalten von Inputs/Outputs, Einschränkungen, Beispiele für Task 25.
- 🔎 **Storybook für Angular in Nx:** Generatoren, Setup, Kopplung mit Browser-Tests.
- 🔎 **Browser-Tests:** Vitest Browser Mode bzw. Playwright gegen Stories, Zusammenspiel mit Nx.
- 🔎 **Routed Dialogs:** Benannter Outlet, Dialog-Container (Material Dialog oder CDK Dialog), Ergebnis zurückgeben, Schließen per Navigation, Zusammenspiel mit Signal-Forms-Control.
- 🔎 **Angular-Version und Material-Version** des Workshop-Repos (bestimmt API-Namen).

## Offene Fragen

- Reicht Material, oder zeigen wir einen reinen CDK-Baustein in Task 10?
- Suche als Funktions-Input oder `resource`-basiert?
- Storybook für Angular in Nx: Generatoren noch nicht recherchiert.
- Wie reif ist Angular Aria aktuell? Reicht es für Task 60?
- Wann liefert Lesson 04 die Daten? Platzhalter-Suche in Task 30 nötig?

## Risiken

- Storybook-Setup ist der größte Einzelposten. Ohne vorgewärmte Umgebung und Checkpoint kann er den Block sprengen.
- Die Summe der Tasks (105 min plus Folien) sprengt 110 min. Kürzungskandidaten in dieser Reihenfolge: Task 30 knapper halten, Formularanbindung in Task 20 streichen, Task 25 auf eine Direktive beschränken.
- Die generische Abstraktion (Task 20) kann zu viel auf einmal werden (Generics, Content Projection, Signal Forms).
- Lesson 02 muss die drei Libraries wirklich leer anlegen und taggen, sonst fehlt hier die Grundlage.
