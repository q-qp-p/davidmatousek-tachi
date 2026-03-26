# Contributing to Tachi

Thank you for your interest in contributing to Tachi.

## How to Contribute

1. **File an Issue**: For bugs, use the [bug report template](.github/ISSUE_TEMPLATE/bug_report.md). For features, use the [feature request template](.github/ISSUE_TEMPLATE/feature_request.md).
2. **Fork and Branch**: Fork the repository and create a feature branch (`NNN-descriptive-name`).
3. **Make Changes**: Follow the existing patterns and governance workflows.
4. **Test**: Run `make check` to verify your changes.
5. **Submit a PR**: Open a pull request against `main` with a clear description of your changes.

## Development Setup

```bash
git clone https://github.com/davidmatousek/tachi.git
cd tachi
make init
make check
```

## Guidelines

- Follow the AOD Triad governance methodology for significant changes.
- Keep commits atomic and use conventional commit messages (`feat:`, `fix:`, `docs:`).
- Update documentation when changing workflows or templates.
- Be respectful and constructive in all interactions.

## Security

If you discover a security vulnerability, **do not open a public issue**. See [SECURITY.md](SECURITY.md) for reporting instructions.

## Code of Conduct

All contributors must follow our [Code of Conduct](CODE_OF_CONDUCT.md).

## Questions?

Open a [discussion](https://github.com/davidmatousek/tachi/discussions) if you have questions about contributing.
