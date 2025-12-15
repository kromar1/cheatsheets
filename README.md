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
# Usage: Type 'notes' -> Fuzzy search notes -> Opens in Neovim
notes() {
    cd ~/dev/cheatsheets && \
    rg --color=always --line-number --no-heading --smart-case . | \
    fzf --ansi \
        --exact \
        --delimiter : \
        --nth=3.. \
        --preview 'bat --style=numbers --color=always --highlight-line {2} {1}' \
        --preview-window 'right:65%:+{2}/2' \
        --bind 'enter:execute(nvim {1} +{2})' \
        --bind 'ctrl-d:preview-half-page-down' \
        --bind 'ctrl-u:preview-half-page-up' \
        --bind 'ctrl-y:preview-up,ctrl-e:preview-down'
}

alias n='notes'
```

### ⚡️ How it works: The `notes` function

This function pipes **Ripgrep** (`rg`) into **Fuzzy Finder** (`fzf`) with a live **Bat** preview. Here is the breakdown of the flags used to make it work seamlessly.

#### 1. The Searcher (`rg`)

Generates the list of all lines in all files.

- `--color=always`: Preserves text colors so `fzf` can display them.
- `--line-number`: Prints the line number (e.g., `50:`) so we can jump to it later.
- `--no-heading`: Forces "flat" output (`file:line:text`) instead of grouping by filename.
- `--smart-case`: Case-insensitive by default, unless you type an uppercase letter.

#### 2. The Selector (`fzf`)

Filters the list interactively.

- `--ansi`: Tells `fzf` to parse and display the colors sent by `rg`.
- `--exact`: Disables "fuzzy" matching (e.g., `git` matches `git`, not `g...i...t`) for better accuracy.
- `--delimiter :`: Splits the input string at every colon.
  - Field 1: Filename
  - Field 2: Line Number
  - Field 3: Text Content
- `--nth=3..`: Limits the search scope to **Field 3 onwards** (the actual text), ignoring filenames and line numbers during filtering.
- `--preview-window 'right:65%:+{2}/2'`: allocates 65% of window width to the preview, automatically scrolls the preview pane to center the matching line (`{2}`) in the middle of the window (`/2`).
- `--bind 'enter:execute(...)'`: Overrides the default Enter key behavior to open Neovim at the specific line number (`+{2}`).
- `--bind 'ctrl-d:preview-half-page-down'`: Scrolls the preview window down by half a page (standard Vim `Ctrl+d`).
- `--bind 'ctrl-u:preview-half-page-up'`: Scrolls the preview window up by half a page (standard Vim `Ctrl+u`).
- `--bind 'ctrl-y:preview-up,ctrl-e:preview-down'`: Fine-grained scrolling. Scrolls the preview window up or down by a single line (matches Vim's `Ctrl+y`/`Ctrl+e` behavior).

#### 3. The Previewer (`bat`)

Runs inside the `fzf` side window.

- `--style=numbers`: Shows line numbers in the preview for context.
- `--color=always`: Forces syntax highlighting in the preview.
- `--highlight-line {2}`: Visually highlights the exact line number where the match was found.

---
