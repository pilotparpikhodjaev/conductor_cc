# Changelog

All notable changes to Conductor for Claude Code will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.0] - 2025-12-27

### Added

- **Skills Integration** - Claude Code skills for automated context-aware assistance
  - `style-guide` - Apply language-specific code style rules (Python, TypeScript, Go, Rust, Swift, Dart)
  - `context-loader` - Efficiently load project context with token optimization
  - `track-manager` - Manage track status, task markers, and phase transitions

- **Subagents Architecture** - Isolated context agents for better token hygiene
  - `context-explorer` - Explore project structure without polluting main context
  - `spec-builder` - Create specifications from requirements
  - `plan-generator` - Generate phased implementation plans
  - `code-reviewer` - Review code for quality, style, and security

- **New Style Guides**
  - Dart (Effective Dart)
  - Rust (API Guidelines)
  - Swift (Apple Guidelines)

- **Token Optimization Protocol** in setup command
  - Respects `.claudeignore` and `.gitignore` patterns
  - Uses `git ls-files` for efficient file discovery
  - Handles large files (>1MB) by reading only first/last 20 lines

### Changed

- Updated plugin.json to v0.2.0
- Enhanced CLAUDE.md with skills and subagents documentation
- Improved marketplace.json with explicit HTTPS URL for broader compatibility

### Fixed

- **Plugin Installation** - Changed source format from `"github"` to `"git"` with explicit HTTPS URL
  - Previously failed for users without SSH keys configured
  - Now works universally via HTTPS cloning

- **Plugin Structure** - Removed redundant `.claude/commands/` directory
  - Plugin format uses `commands/` at root level
  - Was causing duplicate command registration

## [0.1.1] - 2025-12-27

### Added

- Token Optimization Protocol for brownfield project analysis
- Dart, Rust, Swift style guides

### Changed

- Enhanced setup.md with file size triage and ignore file handling

## [0.1.0] - 2025-12-26

### Added

- Initial release of Conductor for Claude Code
- Core commands: `/conductor:setup`, `/conductor:new-track`, `/conductor:implement`, `/conductor:status`, `/conductor:revert`
- Context-driven development workflow
- Track-based project management with spec.md and plan.md
- Built-in style guides: Python, TypeScript, JavaScript, Go, HTML/CSS, General
- Workflow template with TDD, quality gates, and phase checkpointing
- Product and tech-stack definition wizards

---

## Plugin Structure

```
conductor_cc/
├── .claude-plugin/
│   ├── plugin.json          # Plugin manifest (required)
│   └── marketplace.json     # Marketplace distribution config
├── commands/                # Slash commands (at plugin root)
│   ├── setup.md             # /conductor:setup
│   ├── new-track.md         # /conductor:new-track
│   ├── implement.md         # /conductor:implement
│   ├── status.md            # /conductor:status
│   └── revert.md            # /conductor:revert
├── agents/                  # Subagent definitions
│   ├── context-explorer.md
│   ├── spec-builder.md
│   ├── plan-generator.md
│   └── code-reviewer.md
├── skills/                  # Claude Code skills
│   ├── style-guide/SKILL.md
│   ├── context-loader/SKILL.md
│   └── track-manager/SKILL.md
├── templates/               # Project templates
│   ├── workflow.md
│   └── code_styleguides/
│       ├── general.md
│       ├── python.md
│       ├── javascript.md
│       ├── typescript.md
│       ├── go.md
│       ├── rust.md
│       ├── swift.md
│       ├── dart.md
│       └── html-css.md
├── CLAUDE.md                # Plugin context for Claude
├── CHANGELOG.md             # This file
├── README.md                # Documentation
├── LICENSE                  # MIT License
└── CONTRIBUTING.md          # Contribution guidelines
```

## Installation

```bash
# Add marketplace
/plugin marketplace add pilotparpikhodjaev/conductor_cc

# Install plugin
/plugin install conductor
```

## Links

- [GitHub Repository](https://github.com/pilotparpikhodjaev/conductor_cc)
- [Issue Tracker](https://github.com/pilotparpikhodjaev/conductor_cc/issues)
