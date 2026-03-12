# Git Operations Detailed Guide

Reference guide for Git operations. Consulted on-demand when performing git workflows.

## Git Safety Protocol

### Core Principles

🔴 **NEVER:**
- Update git config without explicit request
- Run destructive commands without authorization
- Skip hooks (--no-verify) unless explicitly requested
- Force push to main/master
- Amend commits unless explicitly requested

🔴 **ALWAYS:**
- Create NEW commits rather than amending
- Stage specific files by name (avoid `git add -A` or `git add .`)
- Run hooks by default
- Ask before destructive operations

### Operations Requiring Confirmation

| Operation | Risk Level | Confirmation Required |
|-----------|------------|----------------------|
| `git status/log/diff` | ✅ Safe | No |
| `git add <file>` | ✅ Safe | No |
| `git commit` | ⚠️ Medium | Yes |
| `git push` | ⚠️ Medium | Yes |
| `git push --force` | 🔴 High | Yes + Warning |
| `git reset --hard` | 🔴 High | Yes |
| `git checkout .` | 🔴 High | Yes |
| `git clean -f` | 🔴 High | Yes |
| `git branch -D` | 🔴 High | Yes |

---

## Commit Workflow

### When to Create Commits

🔴 **Only create commits when explicitly requested by the user.**

If unclear, ask first. Never commit proactively.

### Commit Process

**Step 1: Check status and diff**
```bash
git status  # See untracked files (never use -uall flag)
git diff    # See staged and unstaged changes
git log --oneline -10  # Check recent commit style
```

**Step 2: Analyze changes and draft message**
- Summarize nature of changes (feature/enhancement/fix/refactor/test/docs)
- Ensure message reflects actual changes
- Use concise 1-2 sentences focusing on "why" not "what"
- Follow repository's commit message style

**Step 3: Stage and commit**
```bash
# Stage specific files (avoid git add -A or git add .)
git add src/main/java/com/example/UserService.java
git add src/test/java/com/example/UserServiceTest.java

# Commit with message via HEREDOC for proper formatting
git commit -m "$(cat <<'EOF'
Add user validation for empty username

Fixes login issue where empty strings passed validation.
EOF
)"

# Verify success
git status
```

### When Pre-commit Hook Fails

🔴 **CRITICAL: Create NEW commit, never amend!**

When hook fails, the commit did NOT happen. Using `--amend` would modify the PREVIOUS commit, destroying work.

**Correct workflow:**
1. Hook fails → commit didn't happen
2. Fix the issue
3. Re-stage files: `git add <files>`
4. Create NEW commit (not amend)

---

## Pull Request Creation

### PR Creation Process

**Step 1: Understand current state**
```bash
git status  # Check untracked files
git diff    # Check staged/unstaged changes
git branch -vv  # Check if branch tracks remote
git log main..HEAD  # All commits since diverging from main
git diff main...HEAD  # All changes in PR
```

**Step 2: Draft PR title and description**
- Title: Short (<70 chars), describes the change
- Description: Details go here, not in title
- Analyze ALL commits, not just the latest

**Step 3: Push and create PR**
```bash
git checkout -b feature/user-validation
git push -u origin feature/user-validation
gh pr create --title "Add user validation" --body "Detailed description"
```

---

## Destructive Operations

### Reset Operations

- **git reset --soft** — Move HEAD, keep changes staged
- **git reset --mixed** (default) — Move HEAD, unstage changes
- **git reset --hard** — ⚠️ DANGEROUS: Move HEAD, discard all changes

### Force Push

**Never force push to main/master.**

For feature branches, use `--force-with-lease` instead of `--force` to prevent overwriting others' work.

---

## Staging Best Practices

### Files to Never Commit

- `.env` files (secrets)
- `credentials.json` (API keys)
- Large binaries
- IDE-specific files (unless in .gitignore)

### Verify Before Staging

```bash
git status          # See what will be staged
git diff <file>     # Review changes in file
```
