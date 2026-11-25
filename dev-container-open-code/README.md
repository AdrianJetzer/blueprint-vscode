# Dev Container mit OpenCode

Diesen Ordner öffnest du in VS Code und klickst **Reopen in Container**. Einrichtung, Aufbau und Hilfe bei Problemen stehen im [README des Repos](../README.md). Der OpenCode-Teil stammt aus specrun (`infra/devcontainer-opencode`).

## Was drin ist

| Datei | Zweck |
|---|---|
| [`.devcontainer/Dockerfile`](.devcontainer/Dockerfile) | Ubuntu 24.04, Git, curl, ripgrep, jq, Node.js 26 (braucht OpenCode), OpenCode 2.3.2, Benutzer `dev` |
| [`.devcontainer/devcontainer.json`](.devcontainer/devcontainer.json) | bindet `../work` als `/workspace` ein, reicht API-Schlüssel vom Mac durch |
| [`AGENTS.md`](AGENTS.md) | Hausregeln für OpenCode im Container |

| Was | Wo im Container | Zugriff für OpenCode |
|---|---|---|
| `../work` | `/workspace` | lesen und schreiben |
| `AGENTS.md` | `~/.config/opencode/AGENTS.md` | nur lesen |
| Anmeldung und Sitzungen | Volume `dev-container-open-code-data` (`~/.local/share/opencode`) | lesen und schreiben, bleibt bei einem Neubau erhalten |

Anders als in specrun sind die Berechtigungen nicht auf „alles erlauben“ gestellt. OpenCode fragt wie gewohnt nach.

## Anmelden

Zwei Wege, einer genügt:

1. **API-Schlüssel auf dem Mac** in `~/.zshrc`, je nach Anbieter:
   ```bash
   export OPENCODE_API_KEY=…    # Anbieter "opencode", z. B. Big Pickle
   export DEEPSEEK_API_KEY=…    # Anbieter "deepseek"
   export ANTHROPIC_API_KEY=…   # Anbieter "anthropic"
   ```
   VS Code danach ganz beenden (`⌘Q`) und neu starten. Gibt es den Container schon, zusätzlich `⇧⌘P` → **Dev Containers: Rebuild Container**: Die Variablen übernimmt ein Container nur beim Anlegen, ein einfaches **Reopen in Container** behält die alten Werte.

   Prüfen im Container, ohne den Schlüssel anzuzeigen: `echo ${DEEPSEEK_API_KEY:+gesetzt}`

   Heisst dein Schlüssel auf dem Mac anders (z. B. `SPECRUN_DEEPSEEK_API_KEY` aus specrun), reich ihn in `~/.zshrc` weiter: `export DEEPSEEK_API_KEY="$SPECRUN_DEEPSEEK_API_KEY"`
2. **Im Container** einmalig `opencode auth login`. Die Anmeldung liegt dann im Volume.

## Starten

Im Terminal des Containers:

```bash
opencode --standalone
```

Ohne `--standalone` versucht OpenCode einen Hintergrunddienst zu starten, was im Container scheitert (Erkenntnis aus specrun).

Die OpenCode-Version änderst du im `Dockerfile` über `ARG OPENCODE_VERSION`.

## Was noch fehlt

- **VS-Code-Erweiterung:** OpenCode läuft im Terminal, eine Erweiterung ist nicht eingetragen.
