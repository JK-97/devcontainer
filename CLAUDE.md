# Devcontainer Project

Pre-configured dev environment with Go (CGO), Node.js, and vibe-coding tools. MySQL and Redis CLI clients are pre-installed for connecting to remote services.

## Installed Tools

- **Go** 1.24 + gopls, golangci-lint, CGO toolchain (`gcc`, `g++`, `make`, `pkg-config`, `libssl-dev`)
- **Node.js** 22 + npm, pnpm, yarn
- **Database clients**: mysql-client, redis-tools
- **Network**: curl, wget, telnet, netcat, nslookup, ping
- **Git**: git, gh, lazygit, git-delta
- **Productivity**: fzf, ripgrep, fd, bat, jq, yq, zoxide, starship, direnv, entr, tmux

## Performance Volumes

Named Docker volumes overlay write-heavy directories for fast I/O (critical on macOS):

| Directory | Volume |
|-----------|--------|
| `/workspace/node_modules` | `devcontainer-node-modules` |
| `/go/pkg/mod` (GOMODCACHE) | `devcontainer-go-modcache` |
| `/home/vscode/.cache/go-build` (GOCACHE) | `devcontainer-go-buildcache` |

Source code (`/workspace`) uses bind mount with `:cached` — edits sync instantly while reads leverage local volume speed.
