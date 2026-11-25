# Leerer Dev Container mit OpenCode für VS Code
# Basis: Ubuntu 24.04, kein Microsoft-Image. Node.js ist nur dabei, weil OpenCode es braucht.
# Was ein Projekt sonst braucht, kommt später dazu.
FROM ubuntu:24.04

ARG NODE_VERSION=26.13.0
ARG OPENCODE_VERSION=2.3.2
ARG TARGETARCH

# Git und ein paar Werkzeuge, mit denen der Agent arbeitet.
# (Den VS Code Server lädt VS Code beim ersten Verbinden selbst in den Container.)
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      curl ca-certificates xz-utils unzip procps \
      git less ripgrep jq libatomic1 \
 && rm -rf /var/lib/apt/lists/*

# Node.js (offizielles Binärpaket, Architektur automatisch)
RUN case "${TARGETARCH:-$(dpkg --print-architecture)}" in \
      amd64) NARCH=x64 ;; \
      arm64) NARCH=arm64 ;; \
      *) echo "Architektur nicht unterstützt" >&2; exit 1 ;; \
    esac \
 && curl -fsSL "https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-${NARCH}.tar.xz" \
    | tar -xJ -C /usr/local --strip-components=1

# OpenCode (CLI), fest gepinnt wie in specrun. Installiert als root nach /usr/local,
# damit der Agent sich selbst nicht verändern kann.
RUN npm install -g "@opencode/cli@${OPENCODE_VERSION}" \
 && opencode --version \
 && rm -rf /root/.npm

# Ein gewöhnlicher Benutzer ohne Root-Rechte und ohne sudo.
# Das Ubuntu-Image bringt "ubuntu" mit UID 1000 mit; wir ersetzen ihn durch "dev".
RUN userdel -r ubuntu 2>/dev/null || true \
 && useradd --create-home --uid 1000 --shell /bin/bash dev

USER dev
WORKDIR /home/dev
ENV PATH="/home/dev/.local/bin:${PATH}"

# Anmeldung und Sitzungen von OpenCode liegen in ~/.local/share/opencode,
# das ein eigenes Volume wird (siehe devcontainer.json), nicht auf dem Mac.
# In ~/.config/opencode wird AGENTS.md (Hausregeln) nur lesbar eingehängt.
# Berechtigungen bleiben beim Standard von OpenCode: Es fragt nach, bevor es etwas Heikles tut.
RUN mkdir -p /home/dev/.local/share/opencode /home/dev/.config/opencode /home/dev/.vscode-server
