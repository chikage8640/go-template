FROM mcr.microsoft.com/devcontainers/base:ubuntu26.04

RUN apt-get update \
    && apt-get install -y --no-install-recommends build-essential \
    && rm -rf /var/lib/apt/lists/*

# codex が node コマンドを探していたので、用意してあげる
# install nodejs
ARG NODE_VERSION=24.21.0
RUN --mount=type=cache,target=/var/lib/apt/lists,sharing=locked \
    --mount=type=cache,target=/var/cache/apt,sharing=locked \
    apt-get update \
    && apt-get install -y --no-install-recommends \
        curl \
        tar \
        xz-utils \
    && curl -fsSL --compressed "https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-x64.tar.xz" | tar xJ -C /usr/local
ENV PATH=/usr/local/node-v${NODE_VERSION}-linux-x64/bin:${PATH}

RUN npm install -g cspell

# Save history after every command and share it between Bash sessions.
RUN mkdir -p /home/vscode/.cache/bash /home/vscode/.codex /home/vscode/go \
    && chown -R vscode:vscode /home/vscode/.cache /home/vscode/.codex /home/vscode/go \
    && printf '%s\n' '' \
        'export HISTFILE=/home/vscode/.cache/bash/history' \
        'shopt -s histappend' \
        'PROMPT_COMMAND="history -a; ${PROMPT_COMMAND:-:}"' >> /etc/bash.bashrc
