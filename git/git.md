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

## 3. Daily Workflow: Features & PRs

### Starting a New Feature

Standard Git workflow to start work.

```bash
# 1. Update main
git checkout main
git pull

# 2. Create branch
git checkout -b feature/my-cool-feature
```

### Creating a Pull Request

When you are ready to push your code for review.

```bash
# 1. Push the branch (gh will ask to set upstream if needed)
git push -u origin HEAD

# 2. Open the PR interactively (browser not required)
gh pr create
# Follow prompts for Title, Body, and Reviewers
```

### Merging a PR

Merge the current PR without leaving the terminal.

```bash
# Merge the PR associated with the current branch
# --squash : Flattens all commits into one (Recommended)
# -d : Deletes the local and remote branch after merge
gh pr merge --squash -d
```

---

## 4. Code Review

### List or Search PRs

```bash
gh pr list
gh pr search "login"
```

### Check Out Someone Else's PR

Download and switch to a PR to test it locally.

```bash
# Finds the remote, fetches branch, and switches to it
gh pr checkout <PR_NUMBER>
# Example: gh pr checkout 42
```

### View PR Details

```bash
# See the description and checks status of current branch's PR
gh pr view

# See it in the browser
gh pr view --web
```

---

## 5. Repository Management

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
