# GTR Global Hooks Configuration
[[claude-code-architecture]]

publish: true

## What is GTR?

**Git Worktree Runner (GTR)** by CodeRabbit - simplifies parallel development with git worktrees.

- **Repo:** https://github.com/coderabbitai/git-worktree-runner
- **Usage:** `git gtr new feature-branch` creates isolated worktree
- **Config:** Uses git config (not custom config files)

## Global Configuration

Location: `~/.gitconfig`

### File Copying (Already Configured)

```bash
git gtr config add gtr.copy.include "**/.env*" --global
```

Copies `.env`, `.env.local`, `.env.example` to new worktrees automatically.

### postCreate Hooks (Added 2024-12-14)

**Python (uv sync):**
```bash
git gtr config add gtr.hook.postCreate 'if [ -f pyproject.toml ]; then echo "📦 pyproject.toml at root, running uv sync..."; uv sync; elif [ -f backend/pyproject.toml ]; then echo "📦 backend/pyproject.toml found, running uv sync..."; cd backend && uv sync; else echo "ℹ️ No pyproject.toml found (checked: root, backend/). Complex layout? Run manually."; fi' --global
```

**Node (npm install):**
```bash
git gtr config add gtr.hook.postCreate 'if [ -f package.json ]; then echo "📦 package.json at root, running npm install..."; npm install; elif [ -f frontend/package.json ]; then echo "📦 frontend/package.json found, running npm install..."; cd frontend && npm install; else echo "ℹ️ No package.json found (checked: root, frontend/). Complex layout? Run manually."; fi' --global
```

## Design Decisions

### Monorepo-First Pattern
Checks: root → `backend/` (Python) or `frontend/` (Node) → descriptive message

### Fail-Fast Philosophy
- No `|| true` suffix - errors are visible
- If install fails, you see the error
- Subsequent hooks stop on failure (intentional)

### Descriptive Failures
Complex layouts (nested monorepos like paul_template_generation) get a clear message to run manually.

## Hook Behavior

| Scenario | Python Hook | Node Hook |
|----------|-------------|-----------|
| Config at root | `uv sync` runs | `npm install` runs |
| Config in backend/frontend | `cd` + install | `cd` + install |
| No config found | Descriptive message | Descriptive message |
| Install fails | Error visible, stops | Error visible, stops |
| Tool not installed | Error visible | Error visible |

## Verify Current Config

```bash
git config --global --get-all gtr.hook.postCreate
git config --global --get-all gtr.copy.include
```

## Remove Hooks (If Needed)

```bash
git config --global --unset-all gtr.hook.postCreate
```

## Related

- Issue: DaveX2001/deliverable-tracking#241
- GTR Docs: https://github.com/coderabbitai/git-worktree-runner
