---
name: cleanup
version: 0.1.0
description: >
  Schließt eine Session (einen Chat) ab, sodass sie archiviert werden kann und eine neue
  Session nahtlos weitermacht: aufräumen (temporäre Projekte, besonders Testprojekte),
  Integrität herstellen, alles pushen (Konzept-Repo und Paket-Repo), Übergabe schreiben,
  Schlussmeldung mit abgehakter Liste. Verwenden bei "/cleanup", "räum auf und schließ ab",
  "Session abschließen".
---

# Session abschließen

> **Gerüst.** Dieser Skill ist noch nicht spezifiziert (Konzept in `MSC-Cleanup`, Memo 01). Die Abschnitte zeigen nur die Gliederung; ausführen erst nach der Freigabe der Spezifikation.

**Arbeitsweise:** absolute Pfade, `git -C`, nie `cd`. Jeder Schritt nach außen nach Rückfrage.

## 1. Aufräumen

*offen:* was als temporär gilt, Papierkorb statt Löschen (Memo 01, Fragen 2, 3, 8)

## 2. Integrität herstellen

*offen:* `aios --integrity` für die berührten Projekte; Umgang mit „new"-Dateien (Memo 01, Frage 4)

## 3. Pushen

*offen:* eine Rückfrage für alle Pushes oder je Repo; Repos ohne Remote (Memo 01, Fragen 1, 5)

## 4. Übergabe

*offen:* Aufruf von `/handover` oder eigene Übergabe in `@Plan/` (Memo 01, Frage 6)

## 5. Schlussmeldung

Liste der Punkte 1–4 mit ☑, danach: „Die Session kann archiviert werden."
