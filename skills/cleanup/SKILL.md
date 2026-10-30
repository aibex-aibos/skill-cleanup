---
name: cleanup
version: 0.1.0
description: >
  Schließt eine Session (einen Chat) ab, sodass der User sie archivieren kann und eine neue
  Session nahtlos weitermacht: aufräumen (Scratch, Testdateien, Worktrees, Testprojekte,
  Testsitzungen, alles in den Papierkorb), Integrität herstellen, pushen, Übergabe in @Plan/,
  Schlussmeldung mit abgehakter Liste. Verwenden bei "/cleanup", "räum auf und schließ die
  Session ab", "Session abschließen".
---

# Session abschließen

Der Skill schließt die laufende Sitzung in fünf Punkten ab und endet mit einer abgehakten Liste. Er löscht nichts endgültig und archiviert die eigene Sitzung nicht; das tut der User.

**Arbeitsweise:** absolute Pfade, `git -C <repo>`, nie `cd`. **Plan → Rückfrage → Ausführen:** Jede Rückfrage zeigt zuerst, was geschehen soll, und wartet auf ein Ja. Der Aufruf „räum auf" ist kein Ja zu einer Liste. Lehnt der User einen Punkt ab, geh zum nächsten weiter und vermerke ihn in der Schlussmeldung.

## Begriffe

| Begriff | Bedeutung |
|---|---|
| Projekt der Sitzung | der nächste Ordner mit `@/`, vom Arbeitsverzeichnis aufwärts |
| berührtes Projekt | ein Projekt, in dem diese Sitzung geschrieben, committet oder ein Testprojekt angelegt hat |
| Prüfbereich | Projekt der Sitzung mit seinen Unterprojekten und alle berührten Projekte |
| Testprojekt | Unterprojekt `<Präfix>P-Probe-<Name>`, eigenes Git-Repo, im übergeordneten Repo per `.gitignore` ausgeschlossen |
| Testsitzung | Sitzung, deren Arbeitsverzeichnis in einem Testprojekt liegt (auch in `.claude/worktrees/` darunter) |
| Papierkorb | `~/.Trash/<projekt>-cleanup-<YYYY-MM-DD>/` |

## 0. Bestandsaufnahme

1. Bestimme das Projekt der Sitzung. Sammle die berührten Projekte aus zwei Quellen: aus dem Verlauf dieser Sitzung und aus den Repos im Vault mit Commits seit Beginn der Sitzung (`createdAt` aus den Sitzungswerkzeugen, `git -C <repo> log --since=<createdAt>`).
2. **Sitzungswerkzeuge** (nur in der Desktop-App: Sitzungen auflisten, Sitzung lesen, archivieren): Lies die eigene Sitzung (`self`) und liste die anderen. Merke offene Sitzungen, deren Arbeitsverzeichnis im Prüfbereich liegt, und Testsitzungen. Gibt es die Werkzeuge nicht, merke das für die Schlussmeldung.
3. Zeige den Prüfbereich als Liste. Was außerhalb auffällt (z. B. ein fremdes, nicht committetes Projekt im übergeordneten Ordner), erwähne nur; fasse es nicht an.

## 1. Aufräumen

Suche im Prüfbereich nach Kandidaten:

| Art | Kandidat | wie |
|---|---|---|
| Scratch | Dateien im Scratch-Ordner dieser Sitzung | Papierkorb |
| Testdateien, Exporte | nicht committete, nicht ignorierte Dateien, die diese Sitzung selbst angelegt hat | Papierkorb |
| Worktrees | Worktrees ohne eigene Commits gegenüber ihrem Basiszweig | `git -C <repo> worktree remove <pfad>`, dann `git -C <repo> branch -d <zweig>` |
| Testprojekte | Testprojekte | Ordner in den Papierkorb; Zeile `/<testprojekt>/` aus der `.gitignore` des übergeordneten Repos streichen und dort nur diese Datei committen |
| Testsitzungen | Testsitzungen, die nicht gerade arbeiten | archivieren (nur mit Sitzungswerkzeugen) |

- Ein Testprojekt **mit Remote** oder mit Commits, die nicht diese Sitzung gemacht hat, nennst du nur; biete es nicht an.
- Andere nicht committete Dateien gehören zu Punkt 2, nicht hierher.
- In den Papierkorb heißt verschieben (`mv`), nie `rm`. Gibt es den Zielordner schon, hänge `-2`, `-3` … an.

**Rückfrage:** eine Liste aller Kandidaten mit Art und Ziel, ein Ja für die Liste. Ohne Kandidaten melde „nichts aufzuräumen"; der Punkt ist dann erledigt.

## 2. Integrität

1. `aios --integrity <projekt>` für jedes Projekt im Prüfbereich.
2. Nicht committete Dateien: Biete an, sie zu committen (eine Liste je Repo mit Dateien und vorgeschlagener Commit-Nachricht, ein Ja). Was absichtlich nicht ins Repo gehört (z. B. `.aios/`, `@/@!Package/`), schlage für `.gitignore` vor statt zum Committen.
3. Nach dem Ausführen `aios --integrity` noch einmal; das Ergebnis kommt in die Schlussmeldung.

## 3. Pushen

1. Für jedes Repo im Prüfbereich: Remote und Stand (`git -C <repo> status -sb`, `git -C <repo> remote -v`).
2. **Repos ohne Remote:**
   - **Konzept-Repo** (Repo eines Projekts mit `@/`): Biete an, ein Repo nach der Regel *AIOS-Pakete — Vorgaben*, Zeile *Konzept-Repos*, anzulegen (Owner, Name, Sichtbarkeit aus der Regel; fehlt die Regel, frage danach). **Eigene Rückfrage je Repo** mit dem Befehl (`gh repo create <owner>/<name> --private --source <repo> --remote origin`). Bei Ja anlegen und in die Liste der Pushes aufnehmen.
   - **Paket-Repo** (mit `manifest.yaml`, ohne `@/`): nur nennen; ein Paket-Repo im Hub anzulegen gehört zum Veröffentlichen.
   - **Testprojekt** und anderes: nur nennen.
3. **Liste der Pushes:** Tabelle mit Repo, Zweig und Zahl der Commits vor dem Upstream; **ein Ja für alle**. Gepusht werden nur Zweige (`git -C <repo> push`, beim ersten Mal `push -u origin <zweig>`), **nie Tags**.

## 4. Übergabe

1. Läuft eine andere Sitzung, deren Arbeitsverzeichnis im Projekt der Sitzung liegt, nenne sie und frage, ob du die Übergabe trotzdem schreibst.
2. **Mit `@Plan/`:** Schreibe `@Plan/Plan.md` fort (Stand, Status, Schritte mit ☑/◐/☐ und Beleg, Offen mit dem nächsten Schritt zuerst, Arbeitsgedächtnis, Einstieg) und ersetze `@Plan/Handover.md` (Datum, Von, Sitzung in einem Satz, Stand am Ende, Empfehlung für den nächsten Chat mit erstem Satz und erster Handlung, Warnungen).
3. **Ohne `@Plan/`:** Hat die Sitzung an einem Vorhaben über mehrere Sitzungen gearbeitet, schlage einen Plan in dieser Form vor; sonst melde „nichts zu übergeben".
4. Entscheidungen dieser Sitzung, die in keinem Memo stehen, nenne und schlage sie als Memo vor.
5. Zeige den neuen Plan, schreibe nach Ja, committe nur `@Plan/` und die neuen Memos. Hat das Repo einen Remote und hat Punkt 3 ein Ja bekommen, pushe auch diesen Commit.

## 5. Schlussmeldung

Zeige zuerst die Liste:

```markdown
| # | Punkt | Ergebnis |
|---|---|---|
| 1 | Aufräumen | ☑ … |
| 2 | Integrität | ☑ … |
| 3 | Pushen | ☑ … |
| 4 | Übergabe | ☑ … |
```

- ☑ mit kurzem Ergebnis, auch wenn nichts zu tun war („nichts aufzuräumen").
- ☐ mit Grund, wenn ein Punkt nicht möglich war oder der User ihn abgelehnt hat.
- Ohne Sitzungswerkzeuge: Satz, dass Testsitzungen und andere Sitzungen nicht geprüft wurden.

Dann der Satz **„Die Session kann archiviert werden."**, bei einem ☐ ergänzt um das, was offen bleibt.

## Grenzen

- Nichts endgültig löschen: kein `rm`, kein Leeren des Papierkorbs, keine Sitzung löschen.
- Die eigene Sitzung nicht archivieren.
- Keine Tags pushen, keine Pakete veröffentlichen, keine Paket-Repos im Hub anlegen.
- Nichts außerhalb des Prüfbereichs ändern.
- **Ausnahme von der Rückfrage je Schritt nach außen:** Die Pushes der Liste in Punkt 3 laufen mit einem Ja. Ein neues Repo anzulegen bleibt eine eigene Rückfrage.
