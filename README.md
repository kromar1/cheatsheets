# ⚡️ Developer Cheatsheets

A personal collection of reference documentation, commands, and workflows for my development environment.

## 📂 Contents

### 💻 Terminal & CLI

- **[Modern Commands](./terminal/terminal.md)**
  - **Search:** `rg` (Ripgrep), `fd`
  - **Navigation:** `zoxide`, `eza`
  - **Utils:** `httpie`, `jq`, `bat`, `delta`
  - **Monitoring:** `htop`, `dust`, `tokei`

### 🐳 Docker & Infrastructure

- **[Docker & Netshoot](./docker/docker.md)**
  - Diagnostics with `netshoot` (dig, nc, nmap)
  - Container networking & inspection
  - Common debugging commands

- **[Docker Compose](./docker/compose.md)**
  - Node.js + MySQL stack setup
  - Managing volumes & persistence
  - Best practices for `.env` and secrets

---

## 🛠 Prerequisites

These cheatsheets are designed for the environment configured in my **[dotfiles](https://github.com/kromar1/dotfiles)**.

Instead of listing tools here, I maintain a central `Brewfile` in that repository.

### Quick Install

If you have my dotfiles cloned, you can ensure you have all the necessary tools (Ripgrep, FZF, Docker, etc.) by running:

```bash
# Installs everything listed in my Brewfile
brew bundle --file=~/dev/dotfiles/Brewfile
```

**[View the full list of tools here](https://github.com/kromar1/dotfiles/blob/main/Brewfile)**

---

## 🚀 Fast Access (The "Notes" Command)

To instantly search and open these cheatsheets from anywhere in the terminal, add this function to your `~/.zshrc`:

```bash
# Usage: Type 'notes' -> Fuzzy select a file -> Opens in Neovim
notes() {
    # CHANGE THIS PATH to where you cloned this repo
    cd ~/dev/cheatsheets && \
    fd --type f | fzf --preview 'glow {}' | xargs nvim
}
```

---
