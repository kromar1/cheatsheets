# Modern Terminal Commands Cheatsheet

## `rg` (Ripgrep)

_The fastest way to search for text inside files._

### Scenario A: Basic Search

Search for a string recursively in the current directory.

```bash
rg "search_term"
```

### Scenario B: Filtered Search

Search for "foo" only inside TypeScript files, ignoring case, showing 3 lines of context.

```bash
rg "foo" -t ts -i -C 3
```

### Options

- **`-t ts`**: **Type**. Only search files ending in `.ts`. (Use `rg --type-list` to see all types).
- **`-i`**: **Ignore Case**. Case-insensitive search.
- **`-C 3`**: **Context**. Show 3 lines of code before and after the match.
- **`-g '*.{js,css}'`**: **Glob**. Manually filter files using glob patterns.
- **`--hidden`**: Search hidden files/folders (ignores `.gitignore` rules by default, use `-u` to ignore those too).

---

## `fd`

_A faster, friendlier alternative to the `find` command._

### Scenario A: Find Files

Find all files containing "config" in their name.

```bash
fd config
```

### Scenario B: Execute Command on Results

Find all `.png` files and delete them.

```bash
fd -e png -x rm
```

### Options

- **`-e png`**: **Extension**. Filter by file extension.
- **`-H`**: **Hidden**. Include hidden files and directories (like `.git`).
- **`-I`**: **No Ignore**. Search inside git-ignored folders (like `node_modules`).
- **`-x <cmd>`**: **Execute**. Runs a command for every search result.
- **`-t d`**: **Type Directory**. Only look for folders, not files.

---

## `fzf` (Fuzzy Finder)

_Interactive filter for command-line output. It turns any list into a searchable menu._

### Scenario A: Pipe & Preview

Select a file from the current directory and open it in Neovim with a live preview window.

```bash
fd --type f | fzf --preview 'bat --style=numbers --color=always {}' | xargs nvim
```

### Scenario B: Shell Integration (Keyboard Shortcuts)

_Note: These work if fzf integration is installed in Zsh/Bash._

- **`Ctrl + R`**: Search command history.
- **`Ctrl + T`**: Paste a file path from the current directory.
- **`cd **<Tab>`\*\*: Fuzzy complete a directory path.

### Options

- **`--preview '...'`**: Runs a command to generate a preview window for the selected item.
- **`--height 40%`**: Opens fzf in a small pane instead of fullscreen.
- **`--reverse`**: Displays the prompt at the top instead of the bottom.
- **`-m`**: **Multi-select**. Select multiple items using `Tab`.

---

## `bat` (Better Cat)

_A `cat` clone with syntax highlighting and Git integration._

### Scenario A: Read a File

Display a file with line numbers and colors.

```bash
bat README.md
```

### Options

- **`-p`**: **Plain**. Turns off grid and line numbers (copy-paste friendly).
- **`-l json`**: **Language**. Force a specific syntax highlighting language.
- **`--theme=Nord`**: Sets the color theme (matches your system).

---

## `eza` (Better Ls)

_A modern, colorful replacement for `ls`._

### Scenario A: List with Icons & Git

Show a long list (permissions, size) with file icons and git status.

```bash
eza -la --icons --git
```

### Scenario B: Tree View

Show a directory tree structure (recursion).

```bash
eza --tree --level=2
```

### Options

- **`-l`**: **Long**. Shows permissions, size, user, date.
- **`-a`**: **All**. Shows hidden files.
- **`--icons`**: Displays file-type icons (requires Nerd Font).
- **`--git`**: Shows git status (New, Modified, Ignored) next to files.
- **`--tree`**: Renders output as a hierarchy tree.

---

## `zoxide` (Better CD)

_A smarter `cd` command that remembers where you go._

### Usage

Jump to a directory by matching part of its name.

```bash
# cd into a path once
z some/long/path/foo/
# zoxide will remember it so next time just do
z foo

z -                # cd into previous directory
zi foo             # cd with interactive selection (using fzf)

z foo<SPACE><TAB>  # show interactive completions
```

### Options

- **`z <name>`**: Jumps to the best match.
- **`zi`**: **Interactive**. Opens fzf to select a directory from your history.
- **`z -`**: Go back to the previous directory.

---

## `pbcopy` & `pbpaste` (macOS Clipboard)

_Pipe terminal output directly to and from your system clipboard._

### Scenario A: Copy File Content

Copy your public SSH key to the clipboard so you can paste it into GitHub.

```bash
cat ~/.ssh/id_ed25519.pub | pbcopy
```

### Scenario B: Paste to File

Paste the contents of your clipboard into a new file.

```bash
pbpaste > new-file.txt
```

### Scenario C: Copy Command Output

Run a command and copy the result immediately (great for sharing errors/logs).

```bash
docker ps | pbcopy
```

---

## `jq` (JSON Processor)

_Filter, map, and prettify JSON data. Essential for API testing._
_(Requires install: `brew install jq`)_

### Scenario A: Prettify Output

Make a messy blob of JSON readable (syntax highlighted and indented).

```bash
echo '{"foo": "bar", "status": 200}' | jq
```

### Scenario B: Extract Specific Data

Grab only the "description" value from a login API response.

```bash
curl -s https://api.sampleapis.com/coffee/hot | jq '.[0].description'
```

### Options

- **`-s`**: **Silent.** Suppress the progress bar and hide other non-error messages
- **`-r`**: **Row.** Output raw strings (without quotes). Useful for piping into other commands.

---

## `lsof` (List Open Files)

_Find out which process is holding a file or port open._

### Scenario A: Check a Port

"Error: Port 3000 is already in use." Find out who is using it.

```bash
lsof -i :3000
```

### Scenario B: Kill the Process

Find the Process ID (PID) on port 3000 and kill it in one command.

```bash
lsof -ti :3000 | xargs kill -9
```

### Options

- **`-i :<port>`**: **Internet**. List processes listening on a specific network port.
- **`-t`**: **Terse**. Output only the PID numbers (no headers/names).
- **`-P`**: **Port**. Don't convert port numbers to names (e.g., show `80` instead of `http`).

---

## `tldr` (Too Long; Didn't Read)

_Community-driven man pages. Gives you examples instead of a manual._
_(Requires install: `brew install tealdeer`)_

### Usage

Forget how to use `tar` to extract a file?

```bash
tldr tar
```

### Description

Displays a short cheat sheet for the command, listing the 5-6 most common usage patterns immediately. Much faster than reading `man tar`.

---

## `curl` (Client URL)

_Transfer data to/from a server. The standard tool for testing APIs._

### Scenario A: View Headers

Check if your server is returning the correct Content-Type or Cache-Control.

```bash
curl -I https://google.com
```

### Scenario B: Send JSON Data (POST)

Test an API endpoint by sending data.

```bash
curl -X POST -H "Content-Type: application/json" -d '{"name":"Mario"}' http://localhost:3000/users
```

### Options

- **`-X`**: **Request**. Set the HTTP request method (GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS)
- **`-I`**: **Head**. Fetch headers only (no body).
- **`-L`**: **Location**. Follow redirects automatically (if 301/302).
- **`-O`**: **Output**. Save the file with its remote name.
- **`-d '...'`**: **Data**. Sends data (defaults to POST).
- **`-H "..."`**: **Header**. Adds a custom request header.

---

## `httpie` (Modern Curl)

_A user-friendly HTTP client. Intuitive UI, JSON support, and colored output._
_(Command: `http`)_

### Scenario A: GET Request

Fetch data from an API (auto-formats JSON response).

```bash
http [https://api.github.com/users/defunkt](https://api.github.com/users/defunkt)
```

### Scenario B: POST JSON Data

Send a POST request with JSON data. (Syntax `key=value` sends a string, `key:=value` sends raw JSON/numbers).

```bash
http POST localhost:3000/users name="Mario" age:=30
```

### Options

- **`-v`**: **Verbose**. Print the whole request and response (headers + body).
- **`-d`**: **Download**. Save the output as a file.
- **`--offline`**: Construct and print the request without actually sending it (great for debugging).

---

## `delta` (Better Git Diff)

_A viewer for git and diff output with syntax highlighting and side-by-side view._

### Scenario A: Compare Two Files

Compare two files with syntax highlighting.

```bash
delta old-config.yml new-config.yml
```

### Scenario B: Git Integration

_Once configured in `.gitconfig`, it works automatically:_

```bash
git diff
```

### Options

- **`-s`**: **Side-by-Side**. Display the diff in two columns.
- **`-n`**: **Line Numbers**. Show absolute line numbers.
- **`--diff-so-fancy`**: Emulate the simpler look of the tool `diff-so-fancy`.

---

## `dust` (Better Du)

_A more intuitive version of `du`. Visualizes disk usage as a tree graph._

### Scenario A: Analyze Current Directory

See exactly which folders are eating your disk space.

```bash
dust
```

### Scenario B: Limit Depth

Only show the top 2 levels of directories to avoid noise.

```bash
dust -d 2
```

### Options

- **`-r`**: **Reverse**. Show biggest files at the bottom (so they don't scroll off-screen).
- **`-p`**: **Path**. Show full paths.
- **`-X`**: **Ignore**. Exclude files/folders matching a specific name.

---

## `tokei` (Code Statistics)

_Count lines of code, split by language. Very fast._

### Scenario A: Project Summary

Get a breakdown of languages and lines of code in the current folder.

```bash
tokei .
```

### Scenario B: Sort by Size

See which languages take up the most code, sorted by number of lines.

```bash
tokei -s code .
```

### Options

- **`-s`**: **Sort.** Sort by files | lines | blanks | comments | code
- **`-e`**: **Exclude.** Ignore specific folders (e.g., `node_modules`).
- **`-f`**: **Files.** Show statistics for individual files, not just language totals.

---

## `glow` (Markdown Renderer)

_Render Markdown on the CLI with themes._

### Scenario A: Read Documentation

Read a README file nicely formatted in your terminal.

```bash
glow README.md
```

### Scenario B: Pager Mode

Open a long file in a scrollable pager (like `less` but pretty).

```bash
glow -p docs/api.md
```

### Options

- **`-p`**: **Pager**. Opens interactive scroll mode.
- **`-w <n>`**: **Width**. Force word wrapping at a specific column width.
- **`--style`**: Choose a style (e.g., `dark`, `light`, `notty`).

---

## `sd` (Search & Displace)

_A modern, intuitive replacement for `sed`. Find and replace text._

### Scenario A: Simple Replace

Replace "localhost" with "127.0.0.1" in a specific file.

```bash
sd 'localhost' '127.0.0.1' config.js
```

### Scenario B: Regex Replace

Change `const x = 1` to `let x = 1` using capture groups.

```bash
sd 'const (\w+)' 'let $1' app.js
```

### Options

- **`-s`**: **String Mode**. Treat input as a literal string (disable Regex).
- **`-p`**: **Preview**. Show what _would_ happen without actually changing the file.
- **`-f`**: **Flags**. Add regex flags (e.g., `i` for case-insensitive).

---

## `htop` (Process Viewer)

_Interactive process viewer. Better than `top`._

### Scenario A: System Monitor

View CPU, RAM, and active processes.

```bash
htop
```

### Scenario B: Filter by User

Only show processes owned by the current user.

```bash
htop -u $(whoami)
```

### Options

- **`F3`**: Search for a process by name.
- **`F9`**: Kill a selected process.
- **`F5`**: Tree view (shows parent/child relationship of processes).
- **`--sort-key`**: Sort columns by a specific metric (PERCENT_CPU, PERCENT_MEM).
