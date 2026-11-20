# Blueprint: Leerer Dev Container mit Claude Code (VS Code + Podman)

Eine Vorlage, mit der VS Code **und** Claude Code in einem abgeschlossenen Container arbeiten, statt direkt auf deinem Mac.
Claude sieht diesen Projektordner und sonst nichts: keine Musik, keine Dokumente, keine Zugangsdaten.

Der Container ist bewusst leer: Ubuntu, Git und Claude, keine Programmiersprache und kein Beispielprojekt.
Was dein Projekt braucht, ergänzt du im `Dockerfile`.

## Was drin ist

| Datei | Zweck |
|---|---|
| `.devcontainer/Dockerfile` | Bauplan des Containers: Ubuntu 24.04, Git, ein paar Werkzeuge, Claude Code CLI, Benutzer `dev` ohne Root-Rechte |
| `.devcontainer/devcontainer.json` | Sagt VS Code, wie der Container gestartet wird, und installiert darin die Erweiterung **Claude Code for VS Code** (`anthropic.claude-code`) |
| `CLAUDE.md` | Hausregeln für Claude in diesem Projekt |

Das Image ist **kein Microsoft-Image**. Editor und die Erweiterung „Dev Containers“ kommen aber von Microsoft.

## Einmalig einrichten

1. **VS Code** installieren (code.visualstudio.com).
2. In VS Code die Erweiterung **Dev Containers** (`ms-vscode-remote.remote-containers`) installieren. Nur diese eine auf dem Mac; alles andere kommt in den Container.
3. VS Code auf **Podman** umstellen: **Settings** öffnen, nach `dockerPath` suchen, bei **Dev › Containers: Docker Path** eintragen:
   ```
   /opt/podman/bin/podman
   ```
   Der volle Pfad ist nötig: Aus dem Dock gestartete Programme kennen `/opt/podman/bin` nicht.
4. Der Podman-Machine genug Arbeitsspeicher geben (Standard sind 2 GB, das wird mit VS Code Server und Claude knapp) und sie starten:
   ```bash
   /opt/podman/bin/podman machine set --memory 4096
   /opt/podman/bin/podman machine start
   ```

## Projekt im Container öffnen

1. Dieses Repo klonen oder als Vorlage für ein neues Projekt kopieren.
2. In VS Code: **File → Open Folder…** → genau diesen Ordner (nicht den Ordner darüber).
3. Unten rechts erscheint *„Folder contains a Dev Container configuration file. Reopen folder to develop in a container“* → **Reopen in Container**.
   (Alternativ: `⇧⌘P` → **Dev Containers: Reopen in Container**.)
4. Beim ersten Mal dauert es etwas: Image bauen, VS Code Server und die Claude-Erweiterung in den Container laden.
5. Kontrolle: Unten links steht **Dev Container: claude-blueprint-vscode**. Im Terminal zeigt `whoami` den Benutzer `dev`, und `ls /Users` meldet *„No such file or directory“*.
6. Claude öffnen (Symbol in der Seitenleiste oder Terminal: `claude`) und einmalig anmelden. Die Anmeldung landet im Volume `claude-blueprint-vscode-config`.

## Erweitern

Brauchst du eine Programmiersprache, ergänze sie im `Dockerfile` und baue neu (`⇧⌘P` → **Dev Containers: Rebuild Container**). Beispiele:

- Python: im `apt-get install` zusätzlich `python3 python3-venv python3-pip`
- Java: `FROM eclipse-temurin:21-jdk-noble` statt `FROM ubuntu:24.04`
- Passende VS-Code-Erweiterungen kommen in `devcontainer.json` unter `extensions`.

## Gut zu wissen

- **Woran du erkennst, dass du drin bist:** nur am Hinweis unten links und an `whoami`. Sonst sieht alles gleich aus wie auf dem Mac.
- VS Code kopiert deine Git-Konfiguration (Name, E-Mail) in den Container.
- Der Container hat noch **freien Internetzugang**. Eine Firewall ist ein möglicher nächster Schritt.
