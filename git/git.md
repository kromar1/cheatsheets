# Git & GitHub CLI Cheatsheet

## 1. Initial Setup (Fresh Mac)

Run this immediately after installing `gh` to authenticate and generate SSH keys automatically.

```bash
gh auth login
```

**Select these options:**

1.  **Account:** `GitHub.com`
2.  **Protocol:** `SSH`
3.  **Upload your public SSH key?** `Yes`
4.  **Generate a new SSH key?** `Yes` (Let it create a key pair for this machine)
5.  **Authenticate:** `Login with a web browser`

---

## 2. Verification & Configuration

Ensure you are authenticated and forcing SSH for all operations.

### Verify Auth

```bash
# Should return your JSON profile data (login, id, url, etc.)
gh api user
```

### Verify Protocol

```bash
# Should return 'ssh'
gh config get git_protocol
```

### Fix Protocol (If it says https)

```bash
# Force gh to always use git@github.com URLs
gh config set git_protocol ssh
```

---

## 3. Repository Management

### Create a New Repo

Create a repo on GitHub and clone it locally in one step.

```bash
# --public/--private : Visibility
# --clone : Clone it to current directory immediately
gh repo create my-new-project --public --clone
```

### Fork a Repo

Fork a public repo to your account and clone it.

```bash
gh repo fork owner/repo --clone
```

---

## 4. Daily Workflow: Features & PRs

### Starting a New Feature

Standard Git workflow to start work.

```bash
# 1. Update main
git checkout main
git pull

# 2. Create branch
git checkout -b feature/my-cool-feature

```

### Staging and committing some changes

```bash
git add markdown-preview.lua
git commit -m "fix(nvim): use built-in install function for markdown-preview"

git add lazy-lock.json
git commit -m "chore(nvim): update plugin lockfile"
```

[See how to fix accidental commits on main](#fix-main-commits)

### Creating a Pull Request

When you are ready to push your code for review.

```bash
# 1. Push the branch (gh will ask to set upstream if needed)
git push -u origin HEAD

# 2. Open the PR interactively (browser not required)
gh pr create
# Follow prompts for Title, Body, and Reviewers
```

### Code Review

#### List or Search PRs

```bash
gh pr list
gh pr search "login"
```

#### Check Out a PR

Download and switch to a PR to test it locally.

```bash
# Finds the remote, fetches branch, and switches to it
gh pr checkout <PR_NUMBER>
# Example: gh pr checkout 42
```

#### View PR Details

```bash
# See the description and checks status of current branch's PR
gh pr view

# See it in the browser
gh pr view --web
```

### Merging a PR

Merge the current PR without leaving the terminal.

```bash
# Merge the PR associated with the current branch
# --squash : Flattens all commits into one (Recommended)
# -d : Deletes the local and remote branch after merge
gh pr merge --squash -d

# Sync up for next time
git checkout main
git pull
```

---

<a id="fix-main-commits"></a>

## 5. Fix: Moved too fast and committed to main?

If you accidentally committed to `main` instead of a feature branch:

1.  **Create the branch pointer** (Save your work)
    `git branch feature/name-of-branch`
    _(Note: Do not checkout! Stay on main.)_

2.  **Reset main back** (Undo the commits on main)
    `git reset --hard HEAD~X`
    _(Replace X with the number of commits you made, e.g., 1 or 2)_

3.  **Switch to the feature branch** (Continue working)
    `git checkout feature/name-of-branch`
