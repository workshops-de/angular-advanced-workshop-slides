# Lesson 07: Micro-Frontend

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf v2**.

## Ziel

Die Teilnehmenden können begründen, **ab wann** Micro-Frontends sinnvoll sind (und ab wann nicht). Sie verstehen das Grundmodell von Native Federation und binden einen vorbereiteten Remote in die Host-App ein. Sie wissen, wie Shared Dependencies und Versionen ausgehandelt werden.

Beworbene Inhalte: Micro-Front-End, Monolith vs. Modulith, Architektur-Trade-offs.

## Voraussetzungen

Lesson 02: Nx-Workspace, Libraries, Boundaries. Runtime Configuration (Manifest-Idee) ist dort nur Bonus, die Idee muss hier selbst kurz eingeführt werden.

## Schwerpunkt: erst Theorie, dann ein kleiner Praxisteil

Der Inhalt der Lesson ist überwiegend **Konzept**. Die Aufgabe ist bewusst klein und dient dazu, das Modell einmal anzufassen. Das Setup darf keine Zeit fressen (siehe Didaktik).

### Theorie-Bausteine (Folien, in dieser Reihenfolge)

1. **Wann braucht man Micro-Frontends?** Der wichtigste Block, mit Diskussion im Plenum.
   - Treiber sind **organisatorisch**: mehrere Teams, die unabhängig voneinander releasen müssen. Technik allein rechtfertigt es selten.
   - Kosten: Laufzeit-Komplexität, Versionsabstimmung, UX-Konsistenz, Betrieb, Debugging, mehr Infrastruktur.
   - **Alternativen zuerst prüfen:** Monolith mit guter Struktur, **Modulith** (Libraries, Boundaries, Nx, Lesson 02), Monorepo mit `nx affected`.
   - Diskussionsfrage: *Welche Teams und Release-Zyklen gibt es bei euch? Was würde sich konkret ändern, wenn ihr getrennt deployen könntet?*
   - Entscheidungshilfe als Folie: Entscheidungsbaum oder Checkliste ("Müssen Teile unabhängig deployt werden? Sind die Teams wirklich getrennt? Ist die Schnittlinie fachlich stabil?").
2. **Aufbau:** Host (Shell), Remote, was wird exponiert (Komponente oder Routen), Manifest, Laufzeit-Laden.
3. **Native Federation vs. Module Federation:**
   - Module Federation: Webpack-/Rspack-Welt, eigenes Laufzeitmodell mit Share Scopes.
   - Native Federation: gleiche Idee, aber auf **ESM und Import Maps**, bundler-unabhängig (esbuild, Angular Application Builder).
   - Warum das für Angular relevant ist: Application Builder, kein Webpack mehr nötig.
4. **Shared Dependencies und Versionsaushandlung:**
   - Was wird geteilt (`shareAll`), was nicht (`skip`).
   - `singleton`, `strictVersion`, `requiredVersion` (`'auto'`), `eager` kurz.
   - Was passiert bei Konflikt: Warnung oder Fehler (`strictVersion`), welche Version gewinnt.
   - Faustregel: Angular und RxJS müssen Singleton sein, Frameworkversionen bleiben in Host und Remotes kompatibel.
5. **Alles, was zusätzlich wehtut** (kurz, als Ausblick): gemeinsamer State und Kommunikation, Styling, Routing, Tests, Versionierung der Remotes, Fehlerverhalten bei nicht erreichbarem Remote.

## Quellen für die Folien

Stand der Recherche: 2026-11-20. Die Links stammen aus der Web-Suche, **die Artikel sind noch nicht im Volltext gelesen**. Vor dem Übernehmen in Folien prüfen.

### Entscheidung und Konzept (Manfred Steyer, Angular Architects)

- [Micro Frontends with Angular and Native Federation](https://blog.angular.dev/micro-frontends-with-angular-and-native-federation-7623cfc5f413), Gastartikel im Angular-Blog (Feb. 2025). Ausgewogener Überblick über Vor- und Nachteile, Herausforderungen und Umsetzungsansätze. Beste Einstiegsquelle für den "Wann"-Block.
- [Micro Frontends Unmasked: Opportunities, Challenges, Alternatives](https://speakerdeck.com/manfredsteyer/micro-frontends-unmasked-opportunities-challenges-alternatives), Vortragsfolien. Nennt den Modulith ausdrücklich als Alternative. Passt direkt zum Dreiklang Monolith, Modulith, Micro-Frontend.
- [Micro Frontends with Modern Angular (Folien)](https://speakerdeck.com/manfredsteyer/micro-frontends-with-modern-angular), Standalone, esbuild und Native Federation.
- [Strategic Design in the Frontend: Moduliths & Micro Frontends](https://speakerdeck.com/manfredsteyer) (DDD Europe). Nur der Titel gefunden, **konkreten Link noch suchen**.
- [Micro Frontends with Modern Angular, Teil 1: Standalone and esbuild](https://www.angulararchitects.io/blog/micro-frontends-with-modern-angular-part-1-standalone-and-esbuild/), Blogserie mit technischer Vertiefung.
- [Leanpub-Buch zu Micro Frontends](https://leanpub.com/miro-frontend), Link aus der Suche (Schreibweise der URL prüfen).

### Technik

- [Native Federation for Angular (README, npm)](https://www.npmjs.com/package/@angular-architects/native-federation): Befehle, `shareAll`, Optionen.
- [@softarc/native-federation](https://www.npmjs.com/package/@softarc/native-federation): das bundler-unabhängige Kernpaket.
- Offizielle Docs: native-federation.com (Befehle sind im MASTER-PLAN, Abschnitt 9, festgehalten).
- [GitHub-Issue #968](https://github.com/angular-architects/module-federation-plugin/issues/968): bekannte Einschränkung bei der Auflösung von Shared Dependencies zwischen Host und Remote. Nützlich als Praxis-Stolperstein.

### Gegenposition und Einordnung

- [Nx: Micro Frontend Architecture](https://nx.dev/technologies/module-federation/concepts/micro-frontend-architecture): Micro-Frontends lohnen sich, wenn mehrere Teams unabhängig deployen müssen. Bei einem Team eher nicht.
- [DEV: When you should embrace MFEs](https://dev.to/this-is-angular/when-you-should-embrace-mfes-2bng) und [Micro Frontends with Angular: is it worth the complexity?](https://dev.to/rigole/micro-frontends-with-angular-is-it-worth-the-complexity-3pi): kritische Stimmen. Nx-Bibliotheken mit Boundaries lösen viele Teamprobleme ohne Laufzeitkomplexität.

## Tasks

| #   | Task                                   | Status | Min | Lernziel                                                                              |
| --- | -------------------------------------- | ------ | --- | ------------------------------------------------------------------------------------- |
| 10  | Brauchen wir Micro-Frontends?          | neu    | 10  | Trade-offs abwägen, eigene Situation einordnen. Diskussion, ohne Code.                |
| 20  | Remote-Komponente einbinden            | neu    | 20  | Host konfigurieren, Manifest eintragen, eine exponierte Komponente laden.             |
| 30  | Shared Dependencies                    | neu    | 15  | Singleton, Versionskonflikt provozieren und lösen.                                    |
| B1  | Bonus: kompletter Remote mit Routen    | neu    | 20+ | Ein ganzer Anwendungsteil hängt per Route im Host.                                    |

### Vorbereitung (Trainer, vor dem Workshop)

Wir brauchen ein **reines Angular-CLI-Projekt mit mindestens zwei Anwendungen**:

| Anwendung         | Rolle  | Inhalt                                                                 |
| ----------------- | ------ | ---------------------------------------------------------------------- |
| BookMonkey-Client | Host   | Die Bookmonkey-App, in die der Remote eingebunden wird                 |
| Book-Rating       | Remote | Kleine Anwendung für eine **5-Sterne-Bewertung** von Büchern           |

- Der Remote (Book-Rating) ist **fertig vorbereitet**, inklusive Federation-Konfiguration und exponierter Komponente bzw. Routen.
- Beim Host ist die Federation-Konfiguration **nicht** vorhanden, sie ist die Aufgabe der Teilnehmenden.
- Offen: Wie verhält sich das reine CLI-Projekt zum Nx-Workspace aus Lesson 02? Siehe Offene Fragen.

### Aufgabenidee: Remote ist vorbereitet, Teilnehmende konfigurieren nur den Host

Wir stellen einen **fertigen kleinen Remote** (Book-Rating) im Repository bereit. Die Teilnehmenden arbeiten ausschließlich an der Host-Konfiguration in der BookMonkey-Client-Anwendung.

**Stufe 1 (Pflicht, Task 20): eine Angular-Komponente als Remote**
- Der Remote exponiert eine einzelne Komponente: die **5-Sterne-Bewertung** (z. B. `StarRatingComponent` mit Input für das Buch bzw. die ISBN und Output für die Bewertung).
- Einsatz im Host: in der Buchdetailseite oder Buchliste neben dem Buch.
- Host: Federation initialisieren, Eintrag im `federation.manifest.json`, Komponente laden und im Template oder in einer lazy Route einbinden.
- Verständnisfrage: *Was passiert, wenn der Remote nicht läuft?* (Fallback, Fehlerbehandlung.)

**Stufe 2 (Bonus B1): ganzer Anwendungsteil mit Routen**
- Der Remote exponiert eine komplette Routen-Konfiguration (`loadChildren` auf den Remote).
- Der Remote exponiert die Routen der Book-Rating-Anwendung (z. B. Übersicht aller Bewertungen, Bewertung eines Buchs).
- Der Host hängt sie unter einem Pfad ein, z. B. `/ratings`.
- Zusätzliche Themen: Navigation zwischen Host und Remote, gemeinsamer Router, Link-Verhalten.

**Task 30: Shared Dependencies**
- Ausgangslage: Host und Remote teilen Angular als Singleton.
- Konflikt provozieren (z. B. abweichende Version einer geteilten Bibliothek im Remote), Fehlermeldung oder Warnung lesen, mit `requiredVersion` oder Angleichen lösen.
- Das ist ein Wunschszenario und **noch nicht erprobt**.

## Didaktik

Das Setup ist der Engpass. Deshalb:
- **Remote ist vorgefertigt** (Ordner im Repo). Die Teilnehmenden arbeiten nur am Host. Der Remote soll mit einem Befehl starten (`nx serve remote` oder ein Skript für beide).
- **Scaffold per Befehl** nur dort, wo er klein ist (Host-Init).
- **Checkpoint** vor Task 20, damit niemand am Setup hängen bleibt.
- Der Remote kann auch ein Tag-Stand sein, der ausgecheckt wird.
- **Theorie vor Praxis:** Task 10 ist eine Diskussion mit den Folien. Wer den "Wann"-Block versteht, muss danach nicht jede Federation-Option auswendig kennen.

## Recherche-Stand (Technik)

Aus den Docs von native-federation.com (**nicht ausprobiert**):
- Remote: `nx g @angular-architects/native-federation:init --project mfe1 --port 4201 --type remote`
- Host: `nx g @angular-architects/native-federation:init --project shell --port 4200 --type dynamic-host`
- Im Nx-Workspace wird statt `ng g` einfach `nx g` verwendet (Hinweis aus dem Team, **noch nicht ausprobiert**).
- Verdrahtung: `federation.manifest.json` plus lazy Route mit `loadRemoteModule('mfe1', './Component')`.
- Paketname hängt von der Angular-Version ab (`-v4` bei Angular 20 und 21).
- Die Docs raten vom Top-Level-`loadRemoteModule` ab und empfehlen den Loader über DI. Welcher Weg gelehrt wird, ist offen.
- Empfohlene Sharing-Konfiguration laut README: `shareAll({ singleton: true, strictVersion: true, requiredVersion: 'auto' })`.

## Offene Fragen

- **Nx + Native Federation:** Die Docs beschreiben keine Verdrahtung im Nx-Workspace, der Generator lässt sich aber vermutlich einfach mit `nx g` statt `ng g` aufrufen. **Kurzer Spike nötig**, vor allem für: Pfad-Aliasse der Libraries beim Federation-Build, Remote im Nx-Graphen, Start von Host und Remote gemeinsam.
- Wo liegt der Remote (Repo-Ordner oder Hilfsbibliothek)? Entscheidung steht aus. Tendenz: Ordner im Repo.
- **Reines CLI-Projekt oder Nx-Workspace?** Lesson 02 migriert das Workshop-Repo auf Nx. Die Vorbereitung hier sieht ein reines Angular-CLI-Projekt mit zwei Anwendungen vor. Mögliche Auflösungen: (a) Host im Nx-Workspace, Remote als separates CLI-Projekt daneben, (b) ein Checkpoint-Stand für Lesson 07 als eigenes CLI-Projekt, unabhängig vom Nx-Stand, (c) beide Anwendungen im Nx-Workspace. Entscheidung steht aus.
- Wie bekommt der Remote Daten (Buch bzw. ISBN) vom Host: nur per Input, oder spricht er selbst die Bookmonkey-API an? Wo werden die Bewertungen gespeichert (lokal im Remote, API)? Für die Aufgabe reicht Input/Output, ein Speicher ist Bonus.
- Wie verhält sich Task 30 (Versionskonflikt) in der Praxis?
- Wie passen Boundaries zum Remote (`scope:remote`)? Wird der Remote Teil des Nx-Graphen?
- Ist die Zeitschätzung von 45 Minuten realistisch? Mit Folien- und Diskussionszeit für die Theorie eher knapp. Eventuell eigenen Folienblock von 20 bis 30 Minuten einplanen.
- Wie viel der Theorie (Native vs. Module Federation) vertiefen wir, und was bleibt Ausblick?
- Artikel aus der Quellenliste im Volltext lesen und die tragenden Aussagen für die Folien herausziehen.

## Risiken

- Native Federation im Nx-Workspace ist ungeprüft. Der Generator-Aufruf per `nx g` ist wahrscheinlich unkritisch, offen bleiben Pfad-Aliasse geteilter Libraries beim Federation-Build und die Einbindung des Remotes in den Nx-Graphen. Falls es nicht sauber funktioniert, bleibt der Weg über einen separaten Remote außerhalb des Workspace.
- Netzwerk und Ports (4200, 4201) in der Schulungsumgebung.
- Die Theorie kann die Praxis verdrängen. Zeitbox für die Diskussion in Task 10.
