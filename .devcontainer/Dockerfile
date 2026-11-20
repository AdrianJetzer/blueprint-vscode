# Blueprint: Claude Code in einem Dev Container für VS Code
# Basis: Eclipse Temurin (JDK 21 auf Ubuntu 24.04), kein Microsoft-Image.
# Kotlin und Gradle braucht es hier nicht: Der Gradle-Wrapper (./gradlew)
# holt beides beim ersten Bau selbst.
FROM eclipse-temurin:21-jdk-noble

# Git und ein paar Werkzeuge, mit denen Claude arbeitet.
# (Den VS Code Server lädt VS Code beim ersten Verbinden selbst in den Container.)
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      curl ca-certificates unzip procps \
      git less ripgrep jq \
 && rm -rf /var/lib/apt/lists/*

# Ein gewöhnlicher Benutzer ohne Root-Rechte und ohne sudo.
# Das Ubuntu-Image bringt "ubuntu" mit UID 1000 mit; wir ersetzen ihn durch "dev".
RUN userdel -r ubuntu 2>/dev/null || true \
 && useradd --create-home --uid 1000 --shell /bin/bash dev

USER dev
WORKDIR /home/dev

# Claude Code (CLI) mit dem offiziellen Installer.
# Die VS-Code-Erweiterung "Claude Code for VS Code" bringt zwar eine eigene mit,
# die CLI im Pfad erlaubt aber auch "claude" im Terminal.
RUN curl -fsSL https://claude.ai/install.sh | bash
ENV PATH="/home/dev/.local/bin:${PATH}"

# Anmeldung, Einstellungen und Gradle-Cache liegen in eigenen Volumes
# (siehe devcontainer.json), nicht auf dem Mac.
ENV CLAUDE_CONFIG_DIR=/home/dev/.claude
RUN mkdir -p /home/dev/.claude /home/dev/.gradle /home/dev/.vscode-server
