# Conductor for Claude Code

Measure twice, code once.

Conductor brings context-driven development to Claude Code. It turns your workflow into a disciplined lifecycle: Context -> Spec and Plan -> Implement. The result is deliberate, reviewable work that scales across teams and projects.

Inspired by Google: https://developers.googleblog.com/conductor-introducing-context-driven-development-for-gemini-cli/

Repository: https://github.com/pilotparpikhodjaev/conductor_cc

## Why Conductor

- Plan before you build with structured specs and plans
- Maintain shared context with product, tech stack, workflow, and style guides
- Make progress visible with tracks, phases, and task status updates
- Revert by logical work units, not just commit hashes

## Install (local development)

```bash
claude --plugin-dir /path/to/conductor_cc
```

## Install (marketplace)

```text
/plugin marketplace add https://github.com/pilotparpikhodjaev/conductor_cc
/plugin install conductor@conductor-cc
```

## Marketplace

This repository is structured as a Claude Code plugin with `.claude-plugin/plugin.json`, ready for marketplace publishing. Once the marketplace listing is live, anyone will be able to install it from the Claude Code plugin manager. Until then, use `--plugin-dir` for local installation.

## Commands

- `/conductor:setup` - Initialize project context and scaffolding
- `/conductor:new-track` - Create a new track with spec and plan
- `/conductor:implement` - Execute tasks from the current track
- `/conductor:status` - Summarize current progress
- `/conductor:revert` - Git-aware revert of tracks, phases, or tasks

## Contributing

We'd love to accept your patches and contributions to this project.
See `CONTRIBUTING.md` for workflow, expectations, and best practices.

## Support the project

If this project helps you, consider supporting its development.

<a href="https://www.buymeacoffee.com/tenxengineer" target="_blank" rel="noopener noreferrer"><img src="https://img.buymeacoffee.com/button-api/?text=Buy%20me%20a%20coffee&emoji=&slug=tenxengineer&button_colour=FFDD00&font_colour=000000&font_family=Cookie&outline_colour=000000&coffee_colour=ffffff" alt="Buy me a coffee"></a>

## License

MIT. See `LICENSE`.
