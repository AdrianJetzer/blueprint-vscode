# Dev Container mit Claude Code

Diesen Ordner öffnest du in VS Code und klickst **Reopen in Container**. Einrichtung, Aufbau und Hilfe bei Problemen stehen im [README des Repos](../README.md).

## Was drin ist

| Datei | Zweck |
|---|---|
| [`.devcontainer/Dockerfile`](.devcontainer/Dockerfile) | Ubuntu 24.04, Git, curl, ripgrep, jq, Claude Code CLI, Benutzer `dev` |
| [`.devcontainer/devcontainer.json`](.devcontainer/devcontainer.json) | bindet `../work` als `/workspace` ein, installiert **Claude Code for VS Code** |
| [`CLAUDE.md`](CLAUDE.md) | Hausregeln für Claude im Container |

| Was | Wo im Container | Zugriff für Claude |
|---|---|---|
| `../work` | `/workspace` | lesen und schreiben |
| `CLAUDE.md` | `~/.claude/CLAUDE.md` | nur lesen |
| Claude-Anmeldung | Volume `dev-container-claude-code-config` | lesen und schreiben, bleibt bei einem Neubau erhalten |

## Starten

1. Beim ersten Mal dauert es etwas: Image bauen, VS Code Server und die Claude-Erweiterung laden.
2. Claude öffnen (Symbol in der Seitenleiste oder `claude` im Terminal) und einmalig anmelden.

## Wenn es klemmt

| Meldung | Ursache und Lösung |
|---|---|
| *„Claude Code did not finish starting“* | Zu wenig Arbeitsspeicher in der Podman-Maschine. Mit `podman machine set --memory 4096` erhöhen (Maschine vorher stoppen). |
