# skill-cleanup

Skill `cleanup` von AIbex AIOS, Version 0.1.0: schließt eine Session ab, sodass sie archiviert werden kann und eine neue Session nahtlos weitermacht. Er räumt auf, stellt die Integrität her, pusht, schreibt die Übergabe und meldet zum Schluss mit einer abgehakten Liste.

Weggeräumtes kommt in den Papierkorb, nichts wird endgültig gelöscht. Jede Änderung wird vorher gezeigt und braucht ein Ja; alle Pushes einer Sitzung zusammen brauchen ein Ja, ein neues Repo ein eigenes. Testsitzungen archivieren und andere Sitzungen prüfen kann der Skill nur in der Desktop-App von Claude.

## Holen

```bash
aios --fetch skill-cleanup
```

Aufruf ab dem nächsten Chat mit `/cleanup` oder einem Satz wie „Räum auf und schließ die Session ab".

## Lizenz

Apache License 2.0 (`LICENSE`), Rechteinhaber AIbex KG (`NOTICE`). Die Marken „AIbex" und „AIbex AIOS" sind nicht mitlizenziert.
