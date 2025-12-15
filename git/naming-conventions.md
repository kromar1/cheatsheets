# Git Naming Conventions

## Branch Naming

Format: `category/kebab-case-description`
_Example:_ `feature/add-fuzzy-search`

| Prefix          | Usage                                                | Example                        |
| :-------------- | :--------------------------------------------------- | :----------------------------- |
| **`feature/`**  | Adding new functionality.                            | `feature/dark-mode-toggle`     |
| **`fix/`**      | Fixing a bug or issue.                               | `fix/login-crash`              |
| **`chore/`**    | Maintenance, dependencies, config (no code changes). | `chore/update-neovim-config`   |
| **`docs/`**     | Documentation only (README, comments).               | `docs/add-git-cheatsheet`      |
| **`refactor/`** | Code restructuring without changing behavior.        | `refactor/simplify-auth-logic` |
| **`test/`**     | Adding or modifying tests.                           | `test/add-unit-tests`          |
| **`perf/`**     | Performance improvements.                            | `perf/optimize-image-loading`  |

## Commit Naming

Format: `type(scope): description`
_Example:_ `feat(search): add fuzzy matching logic`

Or for breaking changes

Format: `type(scope)!: description`
_Example:_ `feat(scripts)!: rename 'notes' function to 'sn' (breaking alias)`

This includes commits introducing changes that break backward compatibility (e.g., changing an API, deleting a command, renaming a core function).

**Tips:**

1. **Imperative Mood:** Write it as if you are giving a command.
   - ✅ "add feature"
   - ❌ "added feature"
   - ❌ "adds feature"
2. **Lowercase:** Keep the type and description lowercase.
3. **No Period:** Do not end the subject line with a period.

### Commit Types

| Type           | Usage                                              | Example                                 |
| :------------- | :------------------------------------------------- | :-------------------------------------- |
| **`feat`**     | Corresponds to `feature/`. A new feature.          | `feat: allow users to sign up`          |
| **`fix`**      | Corresponds to `fix/`. A bug fix.                  | `fix: crash when logging out`           |
| **`chore`**    | Corresponds to `chore/`. Build/tooling changes.    | `chore: update dependency versions`     |
| **`docs`**     | Corresponds to `docs/`. Documentation only.        | `docs: correct spelling in README`      |
| **`style`**    | Formatting, missing semi-colons (no code change).  | `style: run prettier on all files`      |
| **`refactor`** | Refactoring code (no behavior change).             | `refactor: simplify date parsing logic` |
| **`test`**     | Adding missing tests or correcting existing tests. | `test: add unit tests for user login`   |
