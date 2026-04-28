FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

ARG GO_VERSION=1.24.4
ARG NODE_VERSION=22.15.0
ARG USERNAME=vscode

ENV DEBIAN_FRONTEND=noninteractive

# ── System packages ──────────────────────────────────────────────
RUN apt-get update && apt-get install -y --no-install-recommends \
    # CGO build chain
    build-essential gcc g++ make pkg-config libssl-dev \
    # Network / debugging
    curl wget telnet netcat-openbsd dnsutils iputils-ping \
    # Database clients
    mysql-client \
    redis-tools \
    # Shell & terminal
    tmux \
    # JSON / YAML processors
    jq yq \
    # Compression
    unzip zip xz-utils \
    # CLI editors
    vim nano \
    # Process / system
    htop procps lsof strace \
    # File browsing
    tree less \
    # Certificates & crypto
    ca-certificates gnupg lsb-release software-properties-common \
    # Fuzzy finder
    fzf \
    # Fast grep (ripgrep)
    ripgrep \
    # Fast find (fd)
    fd-find \
    # Better cat (bat)
    bat \
    # Better diff (delta)
    git-delta \
    # File watcher
    entr \
    # GitHub CLI
    gh \
    # Other utils
    direnv \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# ── Symlink tools with Debian-renamed binaries ──────────────────
RUN ln -sf /usr/bin/batcat      /usr/local/bin/bat && \
    ln -sf /usr/bin/fdfind      /usr/local/bin/fd

# ── Go ──────────────────────────────────────────────────────────
ARG TARGETARCH
RUN curl -fsSL "https://go.dev/dl/go${GO_VERSION}.linux-${TARGETARCH}.tar.gz" \
    | tar -xz -C /usr/local
ENV PATH="/usr/local/go/bin:${PATH}"
ENV GOPATH="/go"
ENV GOMODCACHE="/go/pkg/mod"
ENV GOCACHE="/home/$USERNAME/.cache/go-build"
ENV PATH="${GOPATH}/bin:${PATH}"
RUN mkdir -p "$GOPATH" "$GOCACHE" && chown -R $USERNAME:$USERNAME "$GOPATH" "$GOCACHE"

# ── Node.js (binary distribution, switchable via nvm later) ─────
ARG TARGETARCH
RUN NODE_ARCH="x64"; \
    if [ "$TARGETARCH" = "arm64" ]; then NODE_ARCH="arm64"; fi; \
    curl -fsSL "https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-${NODE_ARCH}.tar.xz" \
    | tar -xJ -C /usr/local --strip-components=1
# Core global tooling
RUN npm install -g npm@latest pnpm yarn

# ── lazygit ─────────────────────────────────────────────────────
ARG TARGETARCH
RUN LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" \
      | grep -Po '"tag_name":\s*"v\K[^"]*') && \
    curl -Lo lazygit.tar.gz \
      "https://github.com/jesseduffield/lazygit/releases/latest/download/lazygit_${LAZYGIT_VERSION}_Linux_${TARGETARCH}.tar.gz" && \
    tar xf lazygit.tar.gz lazygit && \
    install lazygit /usr/local/bin && \
    rm lazygit lazygit.tar.gz

# ── zoxide (smarter cd) ─────────────────────────────────────────
RUN curl -fsSL https://raw.githubusercontent.com/ajeetdsouza/zoxide/main/install.sh | sh && \
    mv /root/.local/bin/zoxide /usr/local/bin/zoxide && \
    rm -rf /root/.local

# ── starship prompt ─────────────────────────────────────────────
RUN curl -fsSL https://starship.rs/install.sh | sh -s -- -y

# ── Post-install cleanup & user setup ───────────────────────────
RUN chown -R $USERNAME:$USERNAME /home/$USERNAME

USER $USERNAME
WORKDIR /workspace
