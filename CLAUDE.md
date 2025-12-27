# Conductor Context

Conductor is a context-driven development framework for Claude Code. It transforms the AI agent into a disciplined project manager that follows a strict protocol: **Context → Spec & Plan → Implement**.

## Core Concepts

### What is a Track?

A **Track** is a high-level unit of work, such as a feature, bug fix, or chore. Each track contains:

- `spec.md` - Detailed requirements and acceptance criteria
- `plan.md` - Phased implementation plan with tasks and sub-tasks
- `metadata.json` - Track metadata (type, status, timestamps)

### Project Context Files

When Conductor is set up, it creates these context files in `conductor/`:

- `product.md` - Product vision, users, goals
- `product-guidelines.md` - Brand voice, visual identity
- `tech-stack.md` - Languages, frameworks, databases
- `workflow.md` - TDD workflow, commit strategy, quality gates
- `code_styleguides/` - Language-specific style guides
- `tracks.md` - Master list of all tracks

## Available Commands

| Command                | Description                                           |
| ---------------------- | ----------------------------------------------------- |
| `/conductor:setup`     | Initialize the Conductor environment for your project |
| `/conductor:new-track` | Create a new feature/bug track with spec and plan     |
| `/conductor:implement` | Execute tasks from a track's plan                     |
| `/conductor:status`    | Display current project progress                      |
| `/conductor:revert`    | Git-aware revert of tracks, phases, or tasks          |

## Understanding References

When a user mentions:

- **"the plan"** → Likely refers to `conductor/tracks.md` or `conductor/tracks/<track_id>/plan.md`
- **"the spec"** → Likely refers to `conductor/tracks/<track_id>/spec.md`
- **"the track"** → Refers to the current active track
- **"the workflow"** → Refers to `conductor/workflow.md`

## Task Status Markers

In `plan.md` and `tracks.md` files:

- `[ ]` - Pending (not started)
- `[~]` - In Progress
- `[x]` - Completed (with commit SHA appended)

## Subagents for Context Hygiene

Conductor provides specialized subagents that run in isolated context windows, preventing main conversation pollution:

| Subagent           | Purpose                     | When to Use             |
| ------------------ | --------------------------- | ----------------------- |
| `context-explorer` | Explore project structure   | Starting work on tracks |
| `spec-builder`     | Create specifications       | `/conductor:new-track`  |
| `plan-generator`   | Create implementation plans | After spec is ready     |
| `code-reviewer`    | Review code changes         | Before commits          |

### How to Use Subagents

Invoke via the Task tool:

```
Use the Task tool with the context-explorer agent to explore this project
```

### Benefits

- **Isolated Context**: Heavy exploration doesn't pollute main conversation
- **Token Efficiency**: Only results returned, not raw file contents
- **Focused Output**: Each subagent returns structured, concise summaries
- **Parallel Execution**: Multiple subagents can run simultaneously

## Available Skills

Conductor provides skills that Claude can invoke automatically based on context:

| Skill            | Description                                                                  |
| ---------------- | ---------------------------------------------------------------------------- |
| `style-guide`    | Apply language-specific code style rules (Python, TS, Go, Rust, Swift, Dart) |
| `context-loader` | Efficiently load project context with token optimization                     |
| `track-manager`  | Manage track status, task markers, and phase transitions                     |

Skills are located in `skills/` and are auto-invoked when relevant to the task.

## Token Optimization

Conductor's context-driven approach involves reading project context files. To minimize token consumption:

- Respect `.claudeignore` and `.gitignore` patterns
- Use `git ls-files` for efficient file listing
- Prioritize manifest files (`package.json`, `Cargo.toml`, etc.)
- Read only first/last 20 lines of files over 1MB
- Use subagents for context-heavy operations
- Use the `context-loader` skill for efficient context loading

## Changelog & Versioning Protocol

**MANDATORY: Before every commit, update CHANGELOG.md with the changes being committed.**

### Versioning Strategy

| Change Type      | Version Scope | Example                       |
| ---------------- | ------------- | ----------------------------- |
| Plugin structure | Plugin        | `plugin.json`, `commands/`    |
| Skills/Subagents | Plugin        | `skills/`, `agents/`          |
| Core features    | Release       | New commands, major refactors |
| Bug fixes        | Patch         | Schema fixes, typos           |
| Documentation    | Patch         | README, CLAUDE.md updates     |
| Breaking changes | Major release | API changes, renamed commands |

### Changelog Format

```markdown
## [X.Y.Z] - YYYY-MM-DD

### Added

- New features

### Changed

- Modifications to existing features

### Fixed

- Bug fixes

### Removed

- Removed features
```

### Pre-Commit Checklist

Before running `git commit`:

1. **Update CHANGELOG.md** with all changes in this commit
2. **Bump version** in relevant files:
   - `plugin.json` → for plugin changes
   - `marketplace.json` → for distribution updates
   - `package.json` → if applicable
3. **Use semantic versioning**: MAJOR.MINOR.PATCH
4. **Include commit type** in message: `feat`, `fix`, `docs`, `refactor`, `chore`

### Example Workflow

```bash
# 1. Make changes
# 2. Update CHANGELOG.md with entry
# 3. Bump version if needed
# 4. Commit with descriptive message
git add .
git commit -m "feat(skills): add new style-guide skill

- Added Python and TypeScript style guides
- Updated CHANGELOG.md with v0.3.0 entry"
```
