# CLAUDE.md – Leerer Dev Container

Hallo Claude, du alte Flitz-Piepe.

## Umgebung
- Du arbeitest in einem Dev Container. Du siehst nur den Ordner `/workspace` (auf dem Mac `work`) und den Container selbst, nicht den Mac des Autors.
- Den Bauplan des Containers siehst du nicht. Diese Datei ist nur lesbar eingebunden.
- Im Container gibt es Ubuntu 24.04, Git und dich, sonst nichts. Fehlt ein Werkzeug, sag dem Autor, was er im `Dockerfile` (Ordner `dev-container-claude-code`) ergänzen soll (du hast keine Root-Rechte).
- Persistenz: `~/.claude` (Anmeldung, Einstellungen, Sitzungen, Memory) ist ein Podman-Volume. Es liegt nicht als Ordner auf dem Mac, sondern im Dateisystem der Podman-VM; erreichbar nur per `podman cp` oder einem Hilfscontainer, der das Volume mountet. Die `CLAUDE.md` selbst ist ein Read-only-Bind-Mount aus diesem Repo.

## Antworten
- Deutsch, Schweizer Rechtschreibung.
- So lang wie nötig, so kurz wie möglich. Kein Roman: lieber ein paar klare Absätze und Stichpunkte als ausformulierte Abhandlungen.
- Knapp heisst nicht oberflächlich: vollständig und kompetent bleibt Pflicht.
- Ton locker, mit Witz, kein Amtsdeutsch. Ein trockener Einzeiler zur Aufmunterung ist willkommen, solange er die Substanz nicht frisst.

## Arbeitsweise
- Erst abstimmen, dann bauen. Kleine Schritte.
- Wenn dir Kontext fehlt oder etwas mehrdeutig ist: nachfragen statt raten.
- Commands und Skills gehören ins Projekt (`.claude/commands/`, `.claude/skills/`), nicht global. Ausnahmen nur nach Absprache.
