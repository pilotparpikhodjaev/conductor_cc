---
name: context-loader
description: Load project context efficiently for Conductor workflows. Use when starting work on a track, implementing features, or needing project context without consuming excessive tokens.
---

# Context Loader Skill

Efficiently load and manage project context for Conductor's context-driven development workflow.

## Trigger Conditions

Use this skill when:

- Starting work on a new track or feature
- User mentions: "load context", "project context", "get context"
- Beginning `/conductor:implement` workflow
- Need to understand project structure without reading all files

## Token Optimization Protocol

### 1. Respect Ignore Files

Before scanning files, check for:

1. `.claudeignore` - Claude-specific ignores
2. `.gitignore` - Standard git ignores

```bash
# Check for ignore files
ls -la .claudeignore .gitignore 2>/dev/null
```

### 2. Efficient File Discovery

Use git for tracked files:

```bash
git ls-files --exclude-standard -co | head -100
```

For directory structure:

```bash
git ls-files --exclude-standard -co | xargs -n 1 dirname | sort -u
```

### 3. Priority Files (Read First)

| Priority | File Type | Examples                                          |
| -------- | --------- | ------------------------------------------------- |
| 1        | Manifests | `package.json`, `Cargo.toml`, `pyproject.toml`    |
| 2        | Conductor | `conductor/product.md`, `conductor/tech-stack.md` |
| 3        | Track     | `conductor/tracks/<id>/spec.md`, `plan.md`        |
| 4        | Config    | `tsconfig.json`, `.env.example`                   |

### 4. Large File Handling

For files over 1MB:

- Read first 20 lines (header/imports)
- Read last 20 lines (exports/summary)
- Skip middle content

## Context Loading Workflow

```
1. Load CLAUDE.md (if exists)
2. Load conductor/product.md (project vision)
3. Load conductor/tech-stack.md (technical context)
4. Load current track spec.md (requirements)
5. Load current track plan.md (tasks)
```

## Response Format

After loading context, summarize:

```
## Project Context Loaded

**Product**: [one-line summary]
**Tech Stack**: [key technologies]
**Current Track**: [track name/id]
**Active Phase**: [current phase]
**Pending Tasks**: [count]
```
