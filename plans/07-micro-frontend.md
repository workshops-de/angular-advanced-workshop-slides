# Lesson 07: Micro-Frontend

> Detailplan zur Lesson. Übergeordnet: [MASTER-PLAN.md](./MASTER-PLAN.md). Status: **Entwurf**.

## Ziel

Die Teilnehmenden binden einen vorbereiteten Remote per Native Federation in die Host-App ein und verstehen Shared Dependencies. Sie können Monolith, Modulith und Micro-Frontend gegeneinander abwägen.

Beworbene Inhalte: Micro-Front-End, Monolith vs. Modulith, Architektur-Trade-offs.

## Voraussetzungen

Lessons 02 und 06: Nx-Workspace, Libraries, Boundaries, Runtime Configuration (Manifest-Idee).

## Tasks

| #   | Task                                         | Status | Min | Lernziel                                                                 |
| --- | -------------------------------------------- | ------ | --- | ------------------------------------------------------------------------ |
| 10  | Monolith vs. Modulith vs. Micro-Frontend     | neu    | 10  | Trade-offs, wann lohnt sich was. Ohne Code.                              |
| 20  | Remote anbinden                              | neu    | 20  | Manifest und Route im Host. Der Remote liegt fertig vor.                 |
| 30  | Shared Dependencies                          | neu    | 15  | Singleton, Versionskonflikt provozieren und lösen                        |

## Didaktik

Das Setup ist der Engpass. Deshalb:
- **Remote ist vorgefertigt** (Ordner im Repo oder aus der Hilfsbibliothek). Die Teilnehmenden arbeiten nur am Host.
- **Scaffold per Befehl** nur dort, wo er klein ist (Host-Init).
- **Checkpoint** vor Task 20, damit niemand am Setup hängen bleibt.
- Der Remote kann auch ein Tag-Stand sein, der ausgecheckt wird.

## Recherche-Stand

Aus den Docs von native-federation.com (**nicht ausprobiert**):
- Remote: `ng g @angular-architects/native-federation:init --project mfe1 --port 4201 --type remote`
- Host: `ng g ...:init --project shell --port 4200 --type dynamic-host`
- Verdrahtung: `federation.manifest.json` plus lazy Route mit `loadRemoteModule('mfe1', './Component')`.
- Paketname hängt von der Angular-Version ab (`-v4` bei Angular 20 und 21).
- Die Docs raten vom Top-Level-`loadRemoteModule` ab und empfehlen den Loader über DI. Welcher Weg gelehrt wird, ist offen.

## Offene Fragen

- **Nx + Native Federation:** Die Docs beschreiben keine Verdrahtung im Nx-Workspace. **Spike nötig, größtes Risiko im Gesamtplan.**
- Wo liegt der Remote (Repo-Ordner oder Hilfsbibliothek)?
- Wie verhält sich Task 30 (Versionskonflikt) in der Praxis? Das ist ein Wunschszenario und noch nicht erprobt.
- Wie passen Boundaries zum Remote (`scope:remote`)?
- Ist die Zeitschätzung von 45 Minuten realistisch? Wahrscheinlich eher knapp.

## Risiken

- Native Federation im Nx-Workspace ist ungeprüft. Falls es nicht sauber funktioniert, bleibt nur der Weg über einen separaten Remote außerhalb des Workspace.
- Netzwerk und Ports (4200, 4201) in der Schulungsumgebung.
