# OpenCode Output Styles

An OpenCode plugin that mimics Claude Code's output styles feature, allowing you to load markdown files with YAML frontmatter to dynamically change the system prompt and agent behavior.

## Installation

Add the plugin to your `opencode.json`:

```json
{
  "plugin": ["@capybearista/opencode-output-styles"]
}
```

*For local testing without publishing, you can link the package or use its absolute path.*

## Usage

1. Create a markdown file in `~/.config/opencode/output-styles/` or `<project-root>/.opencode/output-styles/` with YAML frontmatter:
   ```yaml
   ---
   name: "Pirate Style"
   description: "Talks like a pirate"
   keep-coding-instructions: false
   ---
   # Pirate
   You must respond like a swashbuckling pirate.
   ```
2. In OpenCode, run `/style <filename>` (e.g. `/style pirate`) to activate the style.
3. OpenCode will now inject this style into the system prompt and (if `keep-coding-instructions: false`) mute default coding instructions.
4. Use `/style` to list available styles or `/style clear` to remove the active style.

## License

MPL 2.0