# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2025-02-01

### Added

- Initial release of `generate-rouge-lexer.prompt.md`, based on conventions from [rouge-lexer-spl](https://github.com/seanthegeek/rouge-lexer-spl)
- Generates a complete Ruby gem: gemspec, Gemfile, Rakefile, lexer implementation, entry point, Minitest suite, demo and visual sample files, preview server, `AGENTS.md`, `CLAUDE.md`, `CHANGELOG.md`, `LICENSE`, `.gitignore`, `.vscode/settings.json`
- Mandatory documentation-fetch verification workflow embedded in `AGENTS.md` to prevent hallucinated syntax
- Visual preview server (`bundle exec rake server`) and terminal preview script (`preview.rb`) with `DEBUG=1` token dump mode
- Zero-error-token requirement enforced by both the test suite and the preview server

[0.1.0]: https://github.com/seanthegeek/ai-rouge-lexer-template/releases/tag/v0.1.0
