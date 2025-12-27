---
name: track-manager
description: Manage Conductor tracks, phases, and tasks. Use when working with track status, updating task markers, or navigating between tracks.
---

# Track Manager Skill

Manage the lifecycle of Conductor tracks including status updates, task completion, and phase transitions.

## Trigger Conditions

Use this skill when:

- Checking track status or progress
- Marking tasks as complete
- Transitioning between phases
- User mentions: "track status", "mark complete", "next task", "update plan"

## Track Structure

```
conductor/
├── tracks.md           # Master track list
└── tracks/
    └── <track_id>/
        ├── spec.md         # Requirements
        ├── plan.md         # Phased tasks
        └── metadata.json   # Status, timestamps
```

## Task Status Markers

| Marker | Status      | Description           |
| ------ | ----------- | --------------------- |
| `[ ]`  | Pending     | Not started           |
| `[~]`  | In Progress | Currently working     |
| `[x]`  | Completed   | Done (add commit SHA) |

## Workflow Operations

### Start a Task

```markdown
# Before

- [ ] Implement user authentication

# After (mark in progress)

- [~] Implement user authentication
```

### Complete a Task

```markdown
# After completion (add commit SHA)

- [x] Implement user authentication <!-- abc1234 -->
```

### Update tracks.md

When completing a phase, update `conductor/tracks.md`:

```markdown
## Active Tracks

| Track ID | Type    | Status      | Progress  |
| -------- | ------- | ----------- | --------- |
| auth-001 | feature | in_progress | Phase 2/3 |
```

## Phase Transition Rules

1. All tasks in phase must be `[x]` before moving to next phase
2. Update `metadata.json` with completion timestamp
3. Create commit for phase completion
4. Update `tracks.md` progress column

## Response Format

After track operations:

```
## Track Update

**Track**: [track_id]
**Operation**: [started/completed/updated]
**Phase**: [phase number] - [phase name]
**Progress**: [completed]/[total] tasks
**Next**: [next task description]
```
