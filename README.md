# Claude Code im Dev Container (VS Code + Podman)

Ein leerer Dev Container, in dem **Claude Code** arbeitet, statt direkt auf deinem Mac.
Claude sieht nur einen einzigen Ordner, `work`. Musik, Dokumente, SSH-Schlüssel und alles andere auf deinem Mac gibt es für Claude nicht.

- **Leer:** Ubuntu 24.04, Git und Claude, keine Programmiersprache, kein Beispielprojekt. Was du brauchst, ergänzt du selbst.
- **Abgeschottet:** Im Container gibt es nur `work` als `/workspace`. Den Bauplan des Containers sieht Claude nicht und kann ihn darum nicht ändern.
- **Ohne Root:** Claude läuft als Benutzer `dev`, ohne `sudo`.
- **Ohne Abo:** VS Code, Podman und das Image sind kostenlos. Das Image ist kein Microsoft-Image.

Getestet auf macOS mit Podman 6.2 und VS Code 1.147.

## Schnellstart

```bash
git clone https://github.com/AdrianJetzer/blueprint-vscode.git
mkdir work
```

Dann den Ordner `blueprint-vscode` in VS Code öffnen und **Reopen in Container** klicken. Voraussetzungen und Einrichtung stehen weiter unten.

## So ist es aufgebaut

```
Projekte/
├── blueprint-vscode/   ← dieses Repo, öffnest du in VS Code
└── work/               ← legst du selbst an, hier arbeitet Claude
```

| Was | Wo im Container | Zugriff für Claude |
|---|---|---|
| `work/` | `/workspace` | lesen und schreiben |
| `CLAUDE.md` aus diesem Repo | `~/.claude/CLAUDE.md` | nur lesen |
| Claude-Anmeldung | Volume `claude-blueprint-vscode-config` | lesen und schreiben, bleibt bei einem Neubau erhalten |
| `.devcontainer/`, README, `.git` | – | kein Zugriff |
| Dein Mac (`/Users`, …) | – | kein Zugriff |

- Was in `work` entsteht, gehört nicht zum Blueprint. `work` kann ein eigenes Git-Repo werden.
- Mehrere Blueprints im selben Ordner teilen sich ein `work`. Für getrennte Container nimmst du getrennte Ordner.
- Den Bauplan änderst du auf dem Mac, ausserhalb des Containers. Bindest du in `devcontainer.json` weitere Ordner ein, sieht Claude auch diese.

### Dateien

| Datei | Zweck |
|---|---|
| [`.devcontainer/Dockerfile`](.devcontainer/Dockerfile) | Bauplan: Ubuntu 24.04, Git, curl, ripgrep, jq, Claude Code CLI, Benutzer `dev` |
| [`.devcontainer/devcontainer.json`](.devcontainer/devcontainer.json) | Wie VS Code den Container startet: bindet `../work` ein, prüft vorher, ob es `work` gibt, installiert **Claude Code for VS Code** |
| [`CLAUDE.md`](CLAUDE.md) | Hausregeln für Claude im Container |

## Einmalig einrichten

1. **Podman** installieren (Installer von [podman.io](https://podman.io)), der Podman-Maschine genug Arbeitsspeicher geben und sie starten. Die Standardgrösse von 2 GB ist zu knapp, dann startet Claude nicht.
   ```bash
   /opt/podman/bin/podman machine init
   /opt/podman/bin/podman machine set --memory 4096
   /opt/podman/bin/podman machine start
   ```
2. **VS Code** installieren ([code.visualstudio.com](https://code.visualstudio.com)).
3. In VS Code die Erweiterung **Dev Containers** (`ms-vscode-remote.remote-containers`) installieren. Nur diese eine auf dem Mac, alles andere kommt in den Container.
4. VS Code auf Podman umstellen: **Settings** öffnen, nach `dockerPath` suchen und bei **Dev › Containers: Docker Path** eintragen:
   ```
   /opt/podman/bin/podman
   ```
   Der volle Pfad ist nötig, weil aus dem Dock gestartete Programme `/opt/podman/bin` nicht kennen.

## Container starten

1. Repo klonen und daneben `work` anlegen (siehe Schnellstart).
2. In VS Code **File → Open Folder…** → `blueprint-vscode` öffnen, nicht den Ordner darüber.
3. Unten rechts auf **Reopen in Container** klicken, oder `⇧⌘P` → **Dev Containers: Reopen in Container**.
4. Beim ersten Mal dauert es etwas: Image bauen, VS Code Server und die Claude-Erweiterung laden.
5. Claude öffnen (Symbol in der Seitenleiste oder `claude` im Terminal) und einmalig anmelden.

### Woran du erkennst, dass du drin bist

Von aussen sieht alles gleich aus wie auf dem Mac. Prüf es darum:

- Unten links steht **Dev Container: claude-blueprint-vscode**.
- Im Explorer siehst du nur den Inhalt von `work`.
- Im Terminal:
  ```bash
  pwd; whoami; ls /Users
  ```
  Erwartet: `/workspace`, `dev` und *„No such file or directory“*.

## Erweitern

Ergänze im `Dockerfile`, was du brauchst, und baue neu mit `⇧⌘P` → **Dev Containers: Rebuild Container**. Beispiele:

- **Python:** im `apt-get install` zusätzlich `python3 python3-venv python3-pip`
- **Java:** `FROM eclipse-temurin:21-jdk-noble` statt `FROM ubuntu:24.04`
- **VS-Code-Erweiterungen:** in `devcontainer.json` unter `extensions`

## Wenn es klemmt

| Meldung | Ursache und Lösung |
|---|---|
| *„Bitte zuerst den Ordner ../work anlegen“* | `work` fehlt neben `blueprint-vscode`. Mit `mkdir work` anlegen. |
| *„Workspace does not exist“* | VS Code hat einen alten Container wiederverwendet, der noch eine frühere Einstellung hat. `⇧⌘P` → **Dev Containers: Rebuild Container**. |
| *„Claude Code did not finish starting“* | Zu wenig Arbeitsspeicher in der Podman-Maschine. Mit `podman machine set --memory 4096` erhöhen (Maschine vorher stoppen). |
| Kein **Reopen in Container** | Falscher Ordner geöffnet. VS Code sucht `.devcontainer` nur ganz oben im geöffneten Ordner. |
| *„command not found: podman“* | Das Terminal kennt den Pfad noch nicht. Neues Terminal öffnen oder den vollen Pfad `/opt/podman/bin/podman` verwenden. |

## Was noch fehlt

- **Internet:** Im Container hat Claude weiterhin freien Zugang zum Internet. Eine Firewall wäre der nächste Schritt.
- **Git-Konfiguration:** VS Code kopiert deinen Git-Namen und deine E-Mail in den Container.
