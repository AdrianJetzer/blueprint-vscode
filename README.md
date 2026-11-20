# Blueprint: Claude Code im Dev Container (VS Code + Podman)

Eine Vorlage, mit der VS Code **und** Claude Code in einem abgeschlossenen Container arbeiten, statt direkt auf deinem Mac.
Claude sieht diesen Projektordner und sonst nichts: keine Musik, keine Dokumente, keine Zugangsdaten.

Schwester-Projekt: `../blueprint` für IntelliJ IDEA (Dev Containers dort nur mit Ultimate-Abo).

## Was drin ist

| Datei | Zweck |
|---|---|
| `.devcontainer/Dockerfile` | Bauplan des Containers: JDK 21 (Eclipse Temurin, Ubuntu 24.04), Git, Claude Code CLI, Benutzer `dev` ohne Root-Rechte |
| `.devcontainer/devcontainer.json` | Sagt VS Code, wie der Container gestartet wird und welche Erweiterungen darin installiert werden |
| `settings.gradle.kts`, `app/build.gradle.kts`, `gradle/` | Kotlin-Projekt mit Gradle 9.9.1 (Wrapper), Kotlin 2.4.21, JUnit 5 |
| `app/src/main/kotlin/blueprint/App.kt` | Mini-Programm zum Ausprobieren |
| `app/src/test/kotlin/blueprint/AppTest.kt` | Ein Test |
| `CLAUDE.md` | Hausregeln für Claude in diesem Projekt |

Das Image ist **kein Microsoft-Image**. Editor und die Erweiterung „Dev Containers“ kommen aber von Microsoft.

Im Container installiert VS Code automatisch:

- **Claude Code for VS Code** (`anthropic.claude-code`), von Anthropic
- **Gradle for Java** (`vscjava.vscode-gradle`), von Microsoft
- **Kotlin** (`fwcd.kotlin`), aus der Community. Ehrlich gesagt: Kotlin ist in VS Code deutlich schwächer als in IntelliJ.

## Einmalig einrichten

1. **VS Code** installieren (code.visualstudio.com).
2. In VS Code die Erweiterung **Dev Containers** (`ms-vscode-remote.remote-containers`) installieren. Nur diese eine auf dem Mac; alles andere kommt in den Container.
3. VS Code auf **Podman** umstellen: **Settings** öffnen, nach `dockerPath` suchen, bei **Dev › Containers: Docker Path** eintragen:
   ```
   /opt/podman/bin/podman
   ```
   Der volle Pfad ist nötig: Aus dem Dock gestartete Programme kennen `/opt/podman/bin` nicht.
4. Der Podman-Machine genug Arbeitsspeicher geben (Standard sind 2 GB; zu wenig für VS Code Server, Gradle und Claude zusammen) und sie starten:
   ```bash
   /opt/podman/bin/podman machine set --memory 6144
   /opt/podman/bin/podman machine start
   ```

## Projekt im Container öffnen

1. In VS Code: **File → Open Folder…** → diesen Ordner.
2. Unten rechts erscheint *„Folder contains a Dev Container configuration file. Reopen folder to develop in a container“* → **Reopen in Container**.
   (Alternativ: `⇧⌘P` → **Dev Containers: Reopen in Container**.)
3. Beim ersten Mal dauert es etwas: Image bauen, VS Code Server und Erweiterungen in den Container laden.
4. Kontrolle: Unten links steht **Dev Container: claude-blueprint-vscode**. Im Terminal zeigt `whoami` den Benutzer `dev`, und `ls /Users` meldet *„No such file or directory“*.
5. Claude öffnen (Symbol in der Seitenleiste oder Terminal: `claude`) und einmalig anmelden. Die Anmeldung landet im Volume `claude-blueprint-vscode-config`.

## Gut zu wissen

- **Woran du erkennst, dass du drin bist:** nur am Hinweis unten links und an `whoami`. Sonst sieht alles gleich aus wie auf dem Mac.
- VS Code kopiert deine Git-Konfiguration (Name, E-Mail) in den Container.
- Der Container hat noch **freien Internetzugang**. Eine Firewall ist ein möglicher nächster Schritt.
