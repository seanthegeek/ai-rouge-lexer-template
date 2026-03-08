# rouge-lexer-template

Reusable GitHub Copilot / Claude Code prompt for scaffolding a complete
[Rouge](https://github.com/rouge-ruby/rouge) lexer gem for any programming or
query language. Based on the conventions established in
[rouge-lexer-spl](https://github.com/seanthegeek/rouge-lexer-spl).

## What it generates

Running the prompt produces a fully wired-up Ruby gem with:

| File | Purpose |
| --- | --- |
| `{{GEM_NAME}}.gemspec` | Gem metadata and dependencies |
| `Gemfile` | Development dependencies |
| `Rakefile` | `bundle exec rake` test task + preview server task |
| `config.ru` | Rack app serving an HTML preview of both samples |
| `preview.rb` | Terminal preview script with optional `DEBUG=1` token dump |
| `lib/rouge/lexer/{{lang}}.rb` | Thin entry-point that loads the lexer |
| `lib/rouge/lexers/{{lang}}.rb` | `RegexLexer` implementation |
| `spec/rouge_lexer_{{lang}}_spec.rb` | Minitest suite (tag, alias, filename, MIME, round-trip, zero-error-token tests) |
| `spec/demos/{{lang}}` | Short demo snippet for Rouge's demo pages |
| `spec/visual/samples/{{lang}}` | Comprehensive sample covering every token type |
| `README.md` | User-facing installation and usage guide |
| `CHANGELOG.md` | Keep-a-Changelog format, Semantic Versioning |
| `AGENTS.md` | Mandatory AI agent directives: documentation URLs, verification workflow, token mapping, gotchas |
| `CLAUDE.md` | One-liner that imports `AGENTS.md` for Claude Code |
| `.vscode/settings.json` | markdownlint config (`MD024` siblings-only) |
| `.gitignore` | Standard Ruby ignores |
| `LICENSE` | MIT |

## Usage

### 1. Copy or open the prompt

Open [generate-rouge-lexer.prompt.md](generate-rouge-lexer.prompt.md) in VS Code.
With the GitHub Copilot extension, you can run it directly as a `.prompt.md` file
(Chat → `#` → select the file, or use **Ask Copilot** from the file explorer).
With Claude Code, paste the content into a new project and run it as an agent prompt.

### 2. Fill in the placeholders

Edit the three placeholders at the top of the prompt before running it:

| Placeholder | Description |
| --- | --- |
| `{{LANGUAGE_NAME}}` | Human-readable language name, e.g. `Splunk SPL` |
| `{{LANGUAGE_SHORT_NAME}}` | A short, lowercase name for the language e.g., `spl` |
| `{{GEM_NAME}}` | The name of the Ruby gem, e.g,  `rouge-lexer-<short-name>` |
| `{{AUTHOR_NAME}}` | Gem author name |
| `{{GITHUB_USERNAME}}` | Your github username |
| `{{DOCUMENTATION_URLS}}` | Bulleted list of official language documentation URLs the AI must fetch |

The AI derives everything else —  class name, tags, file
extensions, MIME type, token mapping, and detection heuristics — from the language
name and the fetched documentation.

### 3. Provide documentation URLs

`{{DOCUMENTATION_URLS}}` is the most important input. List every official
reference page the AI should consult — individual command pages, function
reference pages, syntax guides, etc. The more specific and complete this list,
the more accurate the resulting lexer will be.

The prompt instructs the AI to fetch and read every URL before writing a single
line of lexer code, preventing hallucinated syntax.

### 4. Run and iterate

After the AI scaffolds the project:

```sh
cd {{GEM_NAME}}
bundle config set --local path vendor/bundle && bundle install
bundle exec rake          # tests must pass
bundle exec rake server   # start preview at http://localhost:9292
curl -s http://localhost:9292 | grep 'class="err"'  # must return nothing
```

Iterate until both conditions are met: all tests pass **and** zero error tokens.

## Design principles

These conventions are baked into every generated project:

- **Documentation-grounded**: the AI is forbidden from adding keywords or syntax
  from training data. Every construct must be traced to a fetched documentation URL.
- **Zero error tokens**: the visual preview server is the authoritative test.
  Passing `bundle exec rake` is necessary but not sufficient.
- **Standard Ruby gem layout**: works out of the box with Bundler, RubyGems, and
  Jekyll's plugin discovery.
- **AI-ready**: `AGENTS.md` carries all the context an AI agent needs to safely
  extend the lexer in future sessions. `CLAUDE.md` imports it for Claude Code.
- **Keep a Changelog**: version history is machine- and human-readable from day one.

## Reference project

[rouge-lexer-spl](https://github.com/seanthegeek/rouge-lexer-spl) — the SPL
(Splunk Search Processing Language) lexer that established these conventions.

## License

MIT
