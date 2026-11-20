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

## Musterlösungen neu erzeugen (Abgleich alt ↔ neu)

Die Task-Texte (`lessons/03-signal-store`) stammen aus einem älteren BookMonkey-Client. Die Musterlösungen für den Signal Store gibt es im `sample-solution`-Repo noch **nicht** (kein `@ngrx/*` in `package.json`, keine `solution--store-*`-Commits). Sie werden neu geschrieben, auf Basis des aktuellen Clients (Branch `solution`, Stand nach den Basics-Tasks).

**Vorgehen (Reihenfolge ist verbindlich):**

1. **Vorab-Check (dieser Abschnitt)** gegen den aktuellen Client durchführen und Lücken schließen, *bevor* Solution-Commits entstehen.
2. Task-Texte korrigieren (Namen, Pfade, Verhalten).
3. Pro Task ein Commit `solution--state-<name>` mit dem `solution-creator`-Skill (Rebase auf `solution`, Submodule `sample-solution`).
4. Nach Lesson 02/03 (Nx-Umbau) die Commits auf die `data-access`-Library mappen. Aktueller Check basiert noch auf der Struktur ohne Nx (`src/app/books`).

### Gefundene Abweichungen

| Alt (Task-Text) | Aktueller Client | Konsequenz |
| --- | --- | --- |
| `BookApiClient.getBooks({ pageSize, searchTerm })`, liefert `Observable` | `BooksClient.getAll()` liefert nur eine `httpResource`, ohne Parameter (fix `_start=0&_end=15`) | **Lücke.** `rxMethod` braucht ein Observable. Entweder `BooksClient` um `getAll$(pageSize, searchTerm)` (HttpClient) ergänzen oder die Tasks auf `HttpClient` im Store umstellen. Empfehlung: Observable-Methode ergänzen, `httpResource`-Variante bleibt für die Basics-Lösung bestehen. |
| `book-list.component`, `booksResource` | `BooksPage` (`books-page.ts`), `booksResource = booksClient.getAll()` | Namen in allen Tasks anpassen. Der Store ersetzt `booksResource` und `reload()` (Delete-Flow). |
| `book-detail.component` mit `input('id')`, Route `:id` | `BookDetailPage` mit `input.required<string>()` namens `isbn`, Route `detail/:isbn`; `BookDetail` ist reine Presentational-Komponente | Task 40 umschreiben: Store-Key ist `isbn`, nicht `id`. |
| `currentBook` findet `book.id === actualBookDetailISBN` | `Book` hat `id` (UUID) **und** `isbn` | **Fehler im alten Text.** Vergleich muss `book.isbn` sein. Gilt auch für `selectId` in Task 70 (`book.isbn`, nicht `id`, Route/Client arbeiten mit ISBN). |
| Detail-Seite: `effect` synct Route-ID in den Store | Detail lädt per `getByIsbn(isbn)` (eigene `httpResource`) | Entscheidung nötig: Detail aus dem Store lesen (Liste evtl. nicht geladen bei Deep Link → Nachladen im Store ergänzen) oder `getByIsbn` behalten. Sonst bricht Direktaufruf von `/books/detail/:isbn`. |
| `removeBook` (Task 70) wird vorausgesetzt | Nirgends in den Tasks 10–60 definiert. Löschen passiert in `BooksPage.deleteBook` per `lastValueFrom(client.delete)` + `reload()` | **Lücke.** In Task 20 oder 40 eine `removeBook`-Methode (rxMethod/`patchState`) ergänzen, sonst hängt Task 70 in der Luft. |
| Task 80: neue "Book Search"-Seite mit Suchfeld + API-Suche | `BooksPage` hat bereits Suchfeld (`BookMarkerStore`, clientseitiges Markieren/Filtern mit `bookMatches`). Keine Search-Route | Entscheiden: eigene Route `books/search` neu bauen (Route + Komponente im Task) oder serverseitige Suche in die bestehende Seite einbauen. Die API kann `q` (Volltext), `_page`, `_limit`. |
| `BookMarkerStore` | Handgeschriebener Signal-Service mit `effect` + `localStorage` | **Chance:** als Lerngegenstand nutzen ("Vorher/Nachher": Refactoring auf `signalStore` + `withHooks`), passt zu Task 30. Nicht neu erfinden. |
| `RuntimeConfiguration` | `BooksClient` hat `#baseUrl` hart codiert | Kein Problem, Store hängt nicht daran (siehe Voraussetzungen). |
| `Book` | Valibot-Schema, `publishedAt` als `Date`, `Book` via `InferOutput` | Store-State typisiert mit diesem `Book`; kein Handlungsbedarf. |

### Verträglichkeit der Pakete (geprüft)

- `@ngrx/signals` 22.0.1 und `@ngrx/operators` 22.0.1 passen zu Angular 22.2 (`peer: ^22.0.0`).
- `@angular-architects/ngrx-toolkit` 22.0.0 verlangt zusätzlich `@ngrx/store` als Peer-Dependency. Im Task 60 mit installieren oder Hinweis geben, sonst Peer-Warnung/Fehler bei `npm install`.
- Der Events-Plugin-Import (`@ngrx/signals/events`) und `withDevtools`/`withImmutableState` gegen die installierte Version beim Schreiben der Lösung real ausprobieren, nicht aus den alten Texten übernehmen.
- Der aktuelle Code nutzt den neuen `@Service()`-Decorator statt `@Injectable`. Store-Provisionierung (`providedIn: 'root'` vs. komponentenlokal wie `BookMarkerStore`) bewusst entscheiden und im Task benennen.

### Offene Entscheidungen vor Start der Solutions

1. Observable-API im `BooksClient` ergänzen (Empfehlung) oder `HttpClient` direkt im Store?
2. Detail-Seite: aus dem Store lesen (inkl. Nachladen bei Deep Link) oder `getByIsbn` behalten?
3. Task 80: neue Search-Route oder bestehende `BooksPage` erweitern?
4. Wo entsteht `removeBook`: Task 20 oder 40?

## Kürzungsoptionen

Falls der Block zu lang ist, in dieser Reihenfolge entfernen oder als Bonus markieren:
1. Third Party Plugins (hat schon Bonus-Datei)
2. Entity Management
3. Lifecycle Hooks mit Computed State zusammenlegen

## Offene Fragen

- Reicht ein Folienthema für "zentral vs. dezentral", oder braucht es einen Task?
- Wird klassisches NgRx Store (Redux) nur erwähnt oder gezeigt? Beworben ist Redux/CQS, aktuell liefert nur der Events-Task das Konzept.
- Wie sinnvoll ist die Reihenfolge der acht Tasks im neuen Projektkontext?
