# Contributing to @capybearista/opencode-output-styles

## Setup

```bash
git clone https://github.com/capybearista/opencode-output-styles.git
cd opencode-output-styles
bun install
```

## Development

```bash
bun run check      # Lint and format
bun run typecheck  # Type check
bun test           # Run tests
bun run build      # Compile
```

## Commit Conventions

This project uses [Conventional Commits](https://www.conventionalcommits.org/):

- `feat: add feature`
- `fix: resolve bug`
- `chore: update deps`
- `docs: improve readme`
- `refactor: simplify logic`
- `test: add coverage`

## Pull Requests

1. Fork and branch from `main`
2. Make changes, add tests
3. Run `bun run lint && bun run typecheck && bun test`
4. Submit a PR with a clear description

## License

By contributing, you agree that your contributions will be licensed under the MPL-2.0 License.
