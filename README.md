# Coding-Agenten im Dev Container (VS Code + Podman)

Leere Dev Container, in denen ein Coding-Agent arbeitet statt direkt auf deinem Mac. Zur Wahl stehen zwei:

| Ordner | Agent | Anmeldung |
|---|---|---|
| [`dev-container-claude-code/`](dev-container-claude-code/) | **Claude Code** | Claude-Konto, einmalig im Container |
| [`dev-container-open-code/`](dev-container-open-code/) | **OpenCode** 2.3.2 | API-Schlüssel des Anbieters oder `opencode auth login` |

Beide sehen nur einen einzigen Ordner, `work`. Musik, Dokumente, SSH-Schlüssel und alles andere auf deinem Mac gibt es für den Agenten nicht.

- **Leer:** Ubuntu 24.04, Git und der Agent, kein Beispielprojekt. Was du brauchst, ergänzt du selbst.
- **Abgeschottet:** Im Container gibt es nur `work` als `/workspace`. Den Bauplan sieht der Agent nicht und kann ihn darum nicht ändern.
- **Ohne Root:** Der Agent läuft als Benutzer `dev`, ohne `sudo`.
- **Ohne Abo:** VS Code, Podman und die Images sind kostenlos. Es sind keine Microsoft-Images.

Getestet auf macOS mit Podman 6.2 und VS Code 1.147.

## Schnellstart

```bash
git clone https://github.com/AdrianJetzer/vscode-dev-container.git
```

Dann in VS Code **File → Open Folder…** wählen, `vscode-dev-container/dev-container-claude-code` oder `vscode-dev-container/dev-container-open-code` öffnen und auf **Reopen in Container** klicken. Wichtig ist der Unterordner, nicht das ganze Repo. Voraussetzungen stehen weiter unten.

## So ist es aufgebaut

```
vscode-dev-container/
├── work/                        ← hier arbeitet der Agent, im Container /workspace
├── dev-container-claude-code/   ← öffnest du in VS Code für Claude Code
└── dev-container-open-code/     ← öffnest du in VS Code für OpenCode
```

- Beide Container teilen sich `work`. Was du mit Claude Code baust, kann OpenCode weiterführen und umgekehrt.
- Was in `work` entsteht, gehört nicht zu diesem Repo (siehe `.gitignore`). `work` kann ein eigenes Git-Repo werden.
- Der Agent sieht weder die Container-Ordner noch das `.git` dieses Repos.
- Den Bauplan änderst du auf dem Mac, ausserhalb des Containers. Bindest du in `devcontainer.json` weitere Ordner ein, sieht der Agent auch diese.

## Einmalig einrichten

1. **Podman** installieren (Installer von [podman.io](https://podman.io)), der Podman-Maschine genug Arbeitsspeicher geben und sie starten. Die Standardgrösse von 2 GB ist zu knapp.
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

Wie du den jeweiligen Agenten startest und anmeldest, steht im README des Unterordners.

## Woran du erkennst, dass du drin bist

Von aussen sieht alles gleich aus wie auf dem Mac. Prüf es darum:

- Unten links steht **Dev Container: dev-container-claude-code** bzw. **dev-container-open-code**.
- Im Explorer siehst du nur den Inhalt von `work`.
- Im Terminal:
  ```bash
  pwd; whoami; ls /Users
  ```
  Erwartet: `/workspace`, `dev` und *„No such file or directory“*.

## Erweitern

Ergänze im `Dockerfile` des Unterordners, was du brauchst, und baue neu mit `⇧⌘P` → **Dev Containers: Rebuild Container**. Beispiele:

- **Python:** im `apt-get install` zusätzlich `python3 python3-venv python3-pip`
- **Java:** `FROM eclipse-temurin:21-jdk-noble` statt `FROM ubuntu:24.04`
- **VS-Code-Erweiterungen:** in `devcontainer.json` unter `extensions`

## Wenn es klemmt

| Meldung | Ursache und Lösung |
|---|---|
| Kein **Reopen in Container** | Falscher Ordner geöffnet. VS Code sucht `.devcontainer` nur ganz oben im geöffneten Ordner. Öffne den Unterordner, nicht `vscode-dev-container`. |
| *„Bitte zuerst den Ordner ../work anlegen“* | `work` fehlt im Repo. Im Repo mit `mkdir work` anlegen. |
| *„Workspace does not exist“* | VS Code hat einen alten Container wiederverwendet. `⇧⌘P` → **Dev Containers: Rebuild Container**. |
| Agent startet nicht, zu wenig Speicher | Podman-Maschine stoppen, mit `podman machine set --memory 4096` erhöhen, wieder starten. |
| *„command not found: podman“* | Das Terminal kennt den Pfad noch nicht. Neues Terminal öffnen oder den vollen Pfad `/opt/podman/bin/podman` verwenden. |

## Was noch fehlt

- **Internet:** Im Container hat der Agent weiterhin freien Zugang zum Internet. Eine Firewall wäre der nächste Schritt.
- **Git-Konfiguration:** VS Code kopiert deinen Git-Namen und deine E-Mail in den Container.
