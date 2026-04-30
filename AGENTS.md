# AGENTS.md

## Quick Reference

| Script            | Purpose                        |
| ----------------- | ------------------------------ |
| `bun run check`   | Lint and format (Biome)        |
| `bun run lint`    | Validate without modifying     |
| `bun run ci`      | CI mode (non-mutating)         |
| `bun run typecheck` | Type check                   |
| `bun test`        | Run tests                      |
| `bun run build`   | Compile TypeScript             |

## Code Style

- Zero comments by default. Only add when code isn't self-explanatory.
- No `console.log`. Use structured approaches for logging/debugging.
- Colocate tests with source files (`src/index.test.ts`).
- Imports: builtins first, then external, then relative.

## Modular Structure

As your plugin grows beyond 150 lines, split into focused modules:

```
src/
├── index.ts      # Thin barrel export (keep under 30 lines)
├── hooks/        # Hook implementations
│   └── event.ts
└── tools/        # Custom tool definitions
    └── my-tool.ts
```

Each file should have a single clear purpose. See `create-opencode-plugin` skill for detailed guidelines.

## License

MPL-2.0
