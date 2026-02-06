# Git Workflow

A practical guide to Git branch strategy, commit conventions, pull request workflow, and release process.

## Branch Strategy

### Branch Types

| Branch | Purpose | Base | Merges Into | Naming |
|--------|---------|------|-------------|--------|
| `main` | Production-ready code | - | - | `main` |
| `develop` | Integration branch (optional) | `main` | `main` | `develop` |
| `feature/*` | New features | `main` | `main` | `feature/add-user-auth` |
| `fix/*` | Bug fixes | `main` | `main` | `fix/login-redirect-loop` |
| `hotfix/*` | Urgent production fixes | `main` | `main` | `hotfix/security-patch` |
| `chore/*` | Maintenance, deps, CI | `main` | `main` | `chore/update-eslint` |
| `refactor/*` | Code restructuring | `main` | `main` | `refactor/extract-auth-module` |
| `docs/*` | Documentation only | `main` | `main` | `docs/api-reference` |

### Branch Naming Rules

- Use lowercase with hyphens: `feature/add-user-auth` (not `Feature/AddUserAuth`)
- Include ticket/issue number when applicable: `feature/GH-42-add-user-auth`
- Keep names concise but descriptive
- Delete branches after merging

### Workflow

```
main ─────────────────●────────────────●─────
                     ╱                ╱
feature/auth ──●──●─╱                ╱
                                    ╱
fix/login ──────────────●──●───────╱
```

1. Create a branch from `main`
2. Make commits on your branch
3. Open a pull request
4. Pass CI checks and code review
5. Squash merge (or rebase merge) into `main`
6. Delete the branch

## Commit Conventions

### Format (Conventional Commits)

```
<type>(<scope>): <subject>

[optional body]

[optional footer(s)]
```

### Types

| Type | Description | Example |
|------|-------------|---------|
| `feat` | New feature | `feat(auth): add OAuth2 login` |
| `fix` | Bug fix | `fix(api): handle null response body` |
| `docs` | Documentation only | `docs(readme): update install steps` |
| `style` | Formatting, no logic change | `style: fix indentation in utils` |
| `refactor` | Neither fix nor feature | `refactor(db): extract query builder` |
| `perf` | Performance improvement | `perf(list): virtualize long lists` |
| `test` | Adding or fixing tests | `test(auth): add login flow tests` |
| `chore` | Maintenance tasks | `chore(deps): update typescript to 5.5` |
| `ci` | CI/CD changes | `ci: add node 22 to test matrix` |
| `build` | Build system changes | `build: switch to esbuild` |
| `revert` | Revert a previous commit | `revert: feat(auth): add OAuth2 login` |

### Rules

1. **Subject line**: imperative mood, lowercase, no period, max 72 chars
   - Good: `feat(auth): add password reset flow`
   - Bad: `feat(auth): Added password reset flow.`

2. **Body**: explain "what" and "why", not "how" (the diff shows how)
   ```
   fix(api): handle timeout on health check endpoint

   The /health endpoint was timing out under high load because it
   queried the database synchronously. Changed to return cached
   status with async background refresh.

   Fixes #234
   ```

3. **Breaking changes**: add `!` after type/scope and explain in footer
   ```
   feat(api)!: change auth token format to JWT

   BREAKING CHANGE: The auth token format has changed from opaque
   tokens to JWT. All existing tokens will be invalidated.
   Clients must re-authenticate after this update.
   ```

4. **Reference issues**: use footer keywords
   ```
   Fixes #42
   Closes #42
   Refs #42
   ```

## Pull Request Workflow

### Creating a PR

1. **Title**: follow commit convention format
   ```
   feat(auth): add OAuth2 Google login
   ```

2. **Description template**:
   ```markdown
   ## Summary
   Brief description of what this PR does and why.

   ## Changes
   - Added OAuth2 provider configuration
   - Implemented Google login callback handler
   - Added session token refresh logic

   ## Test Plan
   - [ ] Unit tests for OAuth2 flow
   - [ ] Manual test: Google login on staging
   - [ ] Manual test: token refresh after expiry

   ## Screenshots (if UI change)
   Before | After

   ## Related
   - Closes #42
   - Depends on #41
   ```

3. **Size guidelines**:
   - Ideal: under 400 lines changed
   - Acceptable: under 800 lines
   - Large: over 800 lines (consider splitting)

### Review Process

1. **Author responsibilities**:
   - Self-review before requesting reviews
   - Ensure CI passes
   - Add reviewers and appropriate labels
   - Respond to all review comments promptly

2. **Reviewer responsibilities**:
   - Review within one business day
   - Use comment labels (`blocker:`, `suggestion:`, `nit:`, `question:`)
   - Approve only when all blockers are resolved

3. **Merge requirements**:
   - At least 1 approval (2 for critical paths)
   - All CI checks passing
   - No unresolved blocker comments
   - Branch is up to date with base

### Merge Strategy

**Squash Merge** (recommended for feature branches):
- Combines all commits into a single clean commit
- Keeps `main` history linear and readable
- PR title becomes the commit message

```bash
# GitHub: Use "Squash and merge" button
# CLI equivalent:
git checkout main
git merge --squash feature/add-auth
git commit -m "feat(auth): add OAuth2 Google login (#42)"
```

**Rebase Merge** (when individual commits matter):
- Preserves individual commits but rebases onto main
- Each commit should be meaningful and passing CI

```bash
git checkout feature/add-auth
git rebase main
git checkout main
git merge --ff-only feature/add-auth
```

## Release Process

### Versioning (Semantic Versioning)

```
MAJOR.MINOR.PATCH
  |     |     |
  |     |     └── Bug fixes (backward compatible)
  |     └──────── New features (backward compatible)
  └────────────── Breaking changes
```

| Change Type | Version Bump | Example |
|-------------|-------------|---------|
| Bug fix | PATCH | `1.2.3` -> `1.2.4` |
| New feature | MINOR | `1.2.3` -> `1.3.0` |
| Breaking change | MAJOR | `1.2.3` -> `2.0.0` |

### Release Checklist

1. Ensure `main` is stable (all tests passing)
2. Update version in `package.json`
3. Update `CHANGELOG.md` with changes since last release
4. Create a git tag: `git tag v1.3.0`
5. Push tag: `git push origin v1.3.0`
6. Create GitHub Release from the tag
7. CI/CD handles publishing (npm, Docker, etc.)

### Changelog Format

```markdown
# Changelog

## [1.3.0] - 2025-01-15

### Added
- OAuth2 Google login (#42)
- Password strength indicator (#45)

### Fixed
- Login redirect loop on expired sessions (#43)

### Changed
- Upgraded TypeScript from 5.4 to 5.5

### Breaking Changes
- None
```

## Common Git Commands

### Daily Workflow

```bash
# Start new work
git checkout main
git pull origin main
git checkout -b feature/my-feature

# Save progress
git add -p                    # Stage changes interactively
git commit -m "feat: partial implementation"

# Stay up to date
git fetch origin
git rebase origin/main        # Rebase onto latest main

# Push and create PR
git push -u origin feature/my-feature
gh pr create --title "feat: my feature" --body "Description"
```

### Fixing Mistakes

```bash
# Amend the last commit (before pushing)
git add .
git commit --amend

# Undo last commit but keep changes
git reset --soft HEAD~1

# Discard all local changes (careful!)
git checkout -- .

# Cherry-pick a specific commit
git cherry-pick abc1234
```

### Branch Cleanup

```bash
# Delete local branch
git branch -d feature/done

# Delete remote branch
git push origin --delete feature/done

# Prune stale remote tracking branches
git fetch --prune

# List merged branches (safe to delete)
git branch --merged main
```

## Git Hooks (with Husky)

### Recommended Setup

```json
// package.json
{
  "scripts": {
    "prepare": "husky"
  }
}
```

### Pre-commit Hook

```bash
# .husky/pre-commit
pnpm lint-staged
```

```json
// package.json
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,md}": ["prettier --write"]
  }
}
```

### Commit Message Hook

```bash
# .husky/commit-msg
npx commitlint --edit $1
```

```js
// commitlint.config.js
export default {
  extends: ['@commitlint/config-conventional'],
};
```
