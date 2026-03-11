# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.5] - 2026-03-11

### Changed

- Replaced "scaffolding" with "building" in the opening prompt description

## [0.1.4] - 2026-03-10

### Added

- Added explicit instruction to copy the Documentation section into the generated `AGENTS.md` exactly as provided, without omitting, consolidating, or reordering any URLs

## [0.1.3] - 2026-03-08

### Added

- Added README conventions section to the prompt requiring the Jekyll/GitHub Pages `Gemfile` example to appear before fenced code block examples

## [0.1.2] - 2026-03-08

### Fixed

- Fixed typo: missing opening `{` in `{{LANGUAGE_SHORT_NAME}}` placeholder

## [0.1.1] - 2026-03-08

### Changed

- Added `{{LANGUAGE_SHORT_NAME}}`, `{{GEM_NAME}}`, and `{{GITHUB_USERNAME}}` as explicit placeholders in the prompt and README
- These values were previously derived automatically; making them explicit gives users more control
- Removed `{{LANGUAGE_SHORT_NAME}}` from the "derived values" list in the prompt since it is now a user-supplied placeholder

## [0.1.0] - 2025-02-01

### Added

- Initial release of `generate-rouge-lexer.prompt.md`, based on conventions from [rouge-lexer-spl](https://github.com/seanthegeek/rouge-lexer-spl)
- Generates a complete Ruby gem: gemspec, Gemfile, Rakefile, lexer implementation, entry point, Minitest suite, demo and visual sample files, preview server, `AGENTS.md`, `CLAUDE.md`, `CHANGELOG.md`, `LICENSE`, `.gitignore`, `.vscode/settings.json`
- Mandatory documentation-fetch verification workflow embedded in `AGENTS.md` to prevent hallucinated syntax
- Visual preview server (`bundle exec rake server`) and terminal preview script (`preview.rb`) with `DEBUG=1` token dump mode
- Zero-error-token requirement enforced by both the test suite and the preview server

[0.1.5]: https://github.com/seanthegeek/ai-rouge-lexer-template/compare/v0.1.4...v0.1.5
[0.1.4]: https://github.com/seanthegeek/ai-rouge-lexer-template/compare/v0.1.3...v0.1.4
[0.1.3]: https://github.com/seanthegeek/ai-rouge-lexer-template/compare/v0.1.2...v0.1.3
[0.1.2]: https://github.com/seanthegeek/ai-rouge-lexer-template/compare/v0.1.1...v0.1.2
[0.1.1]: https://github.com/seanthegeek/ai-rouge-lexer-template/compare/v0.1.0...v0.1.1
[0.1.0]: https://github.com/seanthegeek/ai-rouge-lexer-template/releases/tag/v0.1.0
