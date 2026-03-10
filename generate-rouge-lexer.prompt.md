---
agent: 'agent'
description: 'Generate a complete Rouge lexer gem project for a new language'
---

# Create a Rouge Lexer Gem for {{LANGUAGE_NAME}}

You are scaffolding a complete Ruby gem that provides a Rouge syntax-highlighting
lexer for **{{LANGUAGE_NAME}}**. Rouge is the default syntax highlighter used by
Jekyll and GitHub Pages.

**Fill in these values before running this prompt:**

| Placeholder | Description |
| --- | --- |
| `{{LANGUAGE_NAME}}` | Human-readable language name, e.g. `Splunk SPL` |
| `{{LANGUAGE_SHORT_NAME}}` | A short, lowercase name for the language e.g., `spl` |
| `{{GEM_NAME}}` | The name of the Ruby gem, e.g,  `rouge-lexer-<short-name>` |
| `{{AUTHOR_NAME}}` | Gem author name |
| `{{GITHUB_USERNAME}}` | Your github username |
| `{{DOCUMENTATION_URLS}}` | See the AGENTS.md section below | 

Derive all other values from the language name, author name, and documentation:

- **Description** (`{{LANGUAGE_DESCRIPTION}}`): one-line description of the language
- **Class name** (`{{LANGUAGE_CLASS_NAME}}`): Ruby constant, e.g. `SPL` or `KQL`
- **Gem name** (`{{GEM_NAME}}`): `rouge-lexer-<short-name>`
- **Language tags** (`{{LANGUAGE_TAGS}}`): primary tag plus sensible aliases
- **File extensions** (`{{FILE_EXTENSIONS}}`): from documentation or common knowledge
- **MIME type** (`{{MIME_TYPE}}`): IANA-style, e.g. `text/x-spl`
- **GitHub username** (`{{GITHUB_USERNAME}}`): derive from author name, or use `your-github-username` as a placeholder
- **Token mapping**: derive after fetching the documentation (which constructs map to which Rouge token types)
- **Detection heuristics**: derive from distinctive patterns found in the documentation

---

## Instructions

Generate all files listed below. Substitute all derived values throughout.
Do not invent syntax, keywords, functions, or language constructs — derive everything
from the official documentation URLs provided in `{{DOCUMENTATION_URLS}}`.

## README conventions

The Jekyll / GitHub Pages section of [README.md](README.md) **must** include a
`Gemfile` example showing the gem added inside `group :jekyll_plugins do … end`
**before** the fenced code block examples. Do not omit this example or move it
after the code blocks.

---

## Project layout

```
├── .gitignore
├── .vscode/
│   └── settings.json
├── AGENTS.md
├── CHANGELOG.md
├── CLAUDE.md
├── Gemfile
├── LICENSE
├── README.md
├── Rakefile
├── config.ru
├── preview.rb
├── {{GEM_NAME}}.gemspec
├── lib/
│   └── rouge/
│       ├── lexer/
│       │   └── {{LANGUAGE_SHORT_NAME}}.rb      ← entry point (require-only)
│       └── lexers/
│           └── {{LANGUAGE_SHORT_NAME}}.rb      ← lexer implementation
└── spec/
    ├── rouge_lexer_{{LANGUAGE_SHORT_NAME}}_spec.rb
    ├── demos/
    │   └── {{LANGUAGE_SHORT_NAME}}             ← short demo snippet (plain text)
    └── visual/
        └── samples/
            └── {{LANGUAGE_SHORT_NAME}}         ← comprehensive sample (plain text)
```

---

## Files to generate

### `.gitignore`

Standard Ruby `.gitignore` content (Bundler artifacts, gem build output, editor
files, OS files). Include `vendor/`, `*.gem`, `.bundle/`, `coverage/`, `.DS_Store`,
`Thumbs.db`.

---

### `.vscode/settings.json`

```json
{
  "markdownlint.config": {
    "MD024": { "siblings_only": true }
  }
}
```

---

### `LICENSE`

MIT License, year = current year, copyright holder = `{{AUTHOR_NAME}}`.

---

### `Gemfile`

```ruby
# frozen_string_literal: true

source 'https://rubygems.org'

gemspec

gem 'minitest', '~> 5.0'
gem 'rake', '~> 13.0'

# Visual preview server
gem 'rackup'
gem 'puma'
```

---

### `{{GEM_NAME}}.gemspec`

```ruby
# frozen_string_literal: true

Gem::Specification.new do |s|
  s.name        = '{{GEM_NAME}}'
  s.version     = '0.1.0'
  s.summary     = 'Rouge lexer for {{LANGUAGE_NAME}}'
  s.description = 'A Rouge plugin providing syntax highlighting for {{LANGUAGE_DESCRIPTION}}'
  s.authors     = ['{{AUTHOR_NAME}}']
  s.homepage    = 'https://github.com/{{GITHUB_USERNAME}}/{{GEM_NAME}}'
  s.license     = 'MIT'
  s.files       = Dir['lib/**/*.rb'] + Dir['spec/demos/*'] + Dir['spec/visual/samples/*'] + ['README.md']

  s.required_ruby_version = '>= 3.0'

  s.add_dependency 'rouge', '>= 3.0'

  s.metadata = {
    'source_code_uri'   => 'https://github.com/{{GITHUB_USERNAME}}/{{GEM_NAME}}',
    'bug_tracker_uri'   => 'https://github.com/{{GITHUB_USERNAME}}/{{GEM_NAME}}/issues',
    'changelog_uri'     => 'https://github.com/{{GITHUB_USERNAME}}/{{GEM_NAME}}/blob/main/CHANGELOG.md',
    'documentation_uri' => 'https://github.com/{{GITHUB_USERNAME}}/{{GEM_NAME}}/blob/main/README.md'
  }
end
```

---

### `Rakefile`

```ruby
# frozen_string_literal: true

require 'rake/testtask'

Rake::TestTask.new(:spec) do |t|
  t.libs << 'spec'
  t.test_files = FileList['spec/**/*_spec.rb']
end

desc 'Start the visual preview server on port 9292'
task :server do
  sh 'bundle exec rackup -p 9292'
end

task default: :spec
```

---

### `config.ru`

```ruby
# frozen_string_literal: true

require 'rouge'
require_relative 'lib/rouge/lexer/{{LANGUAGE_SHORT_NAME}}'

app = proc do |_env|
  lexer     = Rouge::Lexer.find('{{LANGUAGE_SHORT_NAME}}')
  formatter = Rouge::Formatters::HTML.new
  theme_css = Rouge::Themes::Github.render(scope: '.highlight')

  sample_path = File.join(__dir__, 'spec', 'visual', 'samples', '{{LANGUAGE_SHORT_NAME}}')
  sample = File.exist?(sample_path) ? File.read(sample_path) : '(no visual sample found)'

  demo_path = File.join(__dir__, 'spec', 'demos', '{{LANGUAGE_SHORT_NAME}}')
  demo = File.exist?(demo_path) ? File.read(demo_path) : '(no demo found)'

  highlighted_sample = formatter.format(lexer.lex(sample))
  highlighted_demo   = formatter.format(lexer.lex(demo))

  body = <<~HTML
    <!DOCTYPE html>
    <html>
    <head>
      <title>Rouge Lexer Preview: #{lexer.class.tag}</title>
      <style>#{theme_css} body { font-family: sans-serif; margin: 2em; }</style>
    </head>
    <body>
      <h1>#{lexer.class.title} Lexer Preview</h1>
      <h2>Demo</h2>
      <div class="highlight"><pre>#{highlighted_demo}</pre></div>
      <h2>Visual Sample</h2>
      <div class="highlight"><pre>#{highlighted_sample}</pre></div>
    </body>
    </html>
  HTML

  ['200', { 'content-type' => 'text/html' }, [body]]
end

run app
```

---

### `preview.rb`

```ruby
# frozen_string_literal: true

require 'rouge'
require_relative 'lib/rouge/lexer/{{LANGUAGE_SHORT_NAME}}'

lexer = Rouge::Lexer.find('{{LANGUAGE_SHORT_NAME}}')

sample_path = File.join(__dir__, 'spec', 'visual', 'samples', '{{LANGUAGE_SHORT_NAME}}')
sample = File.read(sample_path)

if ENV['DEBUG']
  lexer.lex(sample) { |tok, val| puts "#{tok.qualname.ljust(30)} #{val.inspect}" }
else
  formatter = Rouge::Formatters::Terminal256.new(Rouge::Themes::Github.new)
  puts formatter.format(lexer.lex(sample))
end
```

---

### `lib/rouge/lexer/{{LANGUAGE_SHORT_NAME}}.rb`

Entry point only — no lexer logic here:

```ruby
# frozen_string_literal: true

require 'rouge'
require File.expand_path('../lexers/{{LANGUAGE_SHORT_NAME}}', __dir__)
```

---

### `lib/rouge/lexers/{{LANGUAGE_SHORT_NAME}}.rb`


- Extend `RegexLexer`.
- Use class-level `Set` caches (lazily initialized with `@ivar ||= Set.new %w(...)`)
  for every keyword/function/constant category.
- Match tokens in the `:root` state in priority order: whitespace → comments →
  strings → operators/punctuation → literals → keywords/identifiers.
- Use sub-states for block comments, double-quoted strings, single-quoted strings,
  and any other paired delimiters the language has.
- The catch-all `\w+` rule at the end of `:root` does set-membership lookups and
  falls through to `Name` for unrecognized words.
- Include `detect?` heuristics derived from distinctive patterns found in the documentation.
- Study the Rouge JSON lexer and SQL lexer for structural patterns before writing.

**Token type reference** (from `rouge/token.rb`):

| Construct | Token |
| --- | --- |
| Language keywords / commands | `Keyword` |
| Operator keywords (AND, OR, NOT…) | `Keyword::Pseudo` |
| Boolean / null constants | `Keyword::Constant` |
| Built-in functions | `Name::Builtin` |
| Built-in / magic fields or variables | `Name::Variable::Magic` |
| Macro / template references | `Name::Function` |
| Unrecognized identifiers | `Name` |
| Arithmetic / comparison operators | `Operator` |
| Brackets, commas, semicolons, pipes | `Punctuation` |
| Integer literals | `Num::Integer` |
| Float literals | `Num::Float` |
| Double-quoted strings | `Str::Double` |
| Single-quoted strings | `Str::Single` |
| Escape sequences inside strings | `Str::Escape` |
| Line comments | `Comment::Single` |
| Block comments | `Comment::Multiline` |

Override the mapping above where the language requires different assignments, based on what you find in the documentation.

**Common gotchas to avoid:**

- Do not add a negative lookahead on `.` to exclude digits — it breaks IP-address
  tokenization. Use a plain `rule %r/\./`.
- Documentation placeholders (e.g., `<<field-name>>`, `<value>`) are **not** real syntax;
  do not add lexer rules for them.
- Only add keywords/functions that appear in the fetched official documentation.
 - Never invent constructs from training data.

---

### `spec/rouge_lexer_{{LANGUAGE_SHORT_NAME}}_spec.rb`

```ruby
# frozen_string_literal: true

require 'minitest/autorun'
require 'rouge'
require 'rouge/lexer/{{LANGUAGE_SHORT_NAME}}'

class RougeLexer{{LANGUAGE_CLASS_NAME}}Test < Minitest::Test
  def setup
    @lexer = Rouge::Lexers::{{LANGUAGE_CLASS_NAME}}.new
  end

  def test_finds_by_tag
    assert_equal Rouge::Lexers::{{LANGUAGE_CLASS_NAME}}, Rouge::Lexer.find('{{LANGUAGE_SHORT_NAME}}')
  end

  # Add one test_finds_by_alias test per alias defined in the lexer.

  def test_guesses_by_filename
    # Add one assertion per file extension defined in the lexer.
  end

  def test_guesses_by_mimetype
    assert_equal Rouge::Lexers::{{LANGUAGE_CLASS_NAME}}, Rouge::Lexer.guess(mimetype: '{{MIME_TYPE}}')
  end

  def test_demo_preserves_input
    demo = load_demo
    output = @lexer.lex(demo).map { |_, val| val }.join
    assert_equal demo, output, 'Lexer output does not reconstruct the demo input'
  end

  def test_sample_preserves_input
    sample = load_sample
    output = @lexer.lex(sample).map { |_, val| val }.join
    assert_equal sample, output, 'Lexer output does not reconstruct the sample input'
  end

  def test_no_error_tokens_in_demo
    demo = load_demo
    errors = collect_errors(demo)
    assert_empty errors, "Demo produced error tokens:\n#{format_errors(errors)}"
  end

  def test_no_error_tokens_in_sample
    sample = load_sample
    errors = collect_errors(sample)
    assert_empty errors, "Visual sample produced error tokens:\n#{format_errors(errors)}"
  end

  private

  def load_demo
    File.read(File.join(__dir__, 'demos', '{{LANGUAGE_SHORT_NAME}}'))
  end

  def load_sample
    File.read(File.join(__dir__, 'visual', 'samples', '{{LANGUAGE_SHORT_NAME}}'))
  end

  def collect_errors(text)
    @lexer.lex(text).select { |tok, _| tok == Rouge::Token::Tokens::Error }
  end

  def format_errors(errors)
    errors.map { |_, val| "  #{val.inspect}" }.join("\n")
  end
end
```

---

### `spec/demos/{{LANGUAGE_SHORT_NAME}}`

A short (5–15 line) plain-text snippet of real {{LANGUAGE_NAME}} code that
demonstrates the most common language constructs. This is shown on Rouge's demo
pages. It must produce **zero error tokens**.

---

### `spec/visual/samples/{{LANGUAGE_SHORT_NAME}}`

A comprehensive plain-text file (50–200 lines) covering every token type the lexer
recognizes: all keyword categories, every function category, constants, operators,
strings, comments, numeric literals, and any special syntax. Every token type
defined in the lexer must have at least one example here. It must produce **zero
error tokens**.

---

### `README.md`

~~~markdown
# {{GEM_NAME}}

A Rouge lexer plugin for {{LANGUAGE_DESCRIPTION}}. Rouge is the default syntax
highlighter for Jekyll (and therefore GitHub Pages). This gem adds
{{LANGUAGE_NAME}} support to Rouge.

## Installation

Install the gem directly:

```sh
gem install {{GEM_NAME}}
```

Or add it to your `Gemfile`:

```ruby
gem '{{GEM_NAME}}'
```

Then run:

```sh
bundle install
```

## Usage

Once installed, Rouge will automatically discover the lexer. You can use
`{{LANGUAGE_SHORT_NAME}}` as the language tag in fenced code blocks (see the
lexer definition for additional aliases):

````markdown
```{{LANGUAGE_SHORT_NAME}}
# your code here
```
````

### Jekyll / GitHub Pages

Add the gem to your site's `Gemfile` inside the `:jekyll_plugins` group:

```ruby
group :jekyll_plugins do
  gem "{{GEM_NAME}}"
end
```

Run `bundle install`, then use the language tag in fenced code blocks. Jekyll
will pick up the lexer automatically via Rouge's plugin discovery.

### Colors

The lexer tells Rouge how to identify tokens. Rouge wraps each token in a `span` tag with a `class` related to that token type. If you want to change how the tokens are highlighted, change themes or add custom CSS. 

## Development

Install dependencies:

```sh
bundle install
```

Run the test suite:

```sh
bundle exec rake
```

Start the visual preview server (available at http://localhost:9292):

```sh
bundle exec rake server
```

Run the terminal preview script:

```sh
ruby preview.rb
```

Enable debug mode to print each token and its value:

```sh
DEBUG=1 ruby preview.rb
```

### Iterative testing workflow

1. Run `bundle exec rake` to check for test failures and error tokens.
2. Start the server with `bundle exec rake server`.
3. In another terminal, check for error tokens in the rendered output:

   ```sh
   curl -s http://localhost:9292 | grep 'class="err"'
   ```

4. Fix any error tokens in `lib/rouge/lexers/{{LANGUAGE_SHORT_NAME}}.rb`.
5. Repeat until no error tokens remain.

## License

MIT
````

~~~

### `CHANGELOG.md`

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - YYYY-MM-DD

### Added

- Initial release of the `{{GEM_NAME}}` gem
- `Rouge::Lexers::{{LANGUAGE_CLASS_NAME}}` lexer with tag `{{LANGUAGE_SHORT_NAME}}`,
  appropriate aliases, filename extensions, and MIME type `{{MIME_TYPE}}`
- Auto-detection heuristics based on common {{LANGUAGE_NAME}} patterns
- Token classification for _(describe your token categories here)_

[0.1.0]: https://github.com/{{GITHUB_USERNAME}}/{{GEM_NAME}}/releases/tag/v0.1.0
```

The changelog follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) format:

- Release headings: `## [version] - YYYY-MM-DD`
- Section headings: `### Added`, `### Changed`, `### Removed`
- Optional third-level category headings: `#### Commands`, `#### Functions`, etc.
- Blank lines must surround all headings (markdownlint requirement).

---

### `CLAUDE.md`

```markdown
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with
code in this repository.

@AGENTS.md
```

---

### `AGENTS.md`

```markdown
# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

## Commands

```sh
bundle config set --local path vendor/bundle && bundle install  # Install dependencies
bundle exec rake        # Run the test suite (default task)
bundle exec rake server # Start visual preview server at http://localhost:9292
ruby preview.rb         # Terminal preview using Github theme
DEBUG=1 ruby preview.rb # Print each token and its type
```

To check for error tokens via the preview server:

```sh
curl -s http://localhost:9292 | grep 'class="err"'
```

## Architecture

This is a Rouge lexer plugin gem for {{LANGUAGE_DESCRIPTION}}. Rouge is the
default syntax highlighter used by Jekyll/GitHub Pages.

**Key files:**

- [lib/rouge/lexers/{{LANGUAGE_SHORT_NAME}}.rb](lib/rouge/lexers/{{LANGUAGE_SHORT_NAME}}.rb) — The lexer implementation (`Rouge::Lexers::{{LANGUAGE_CLASS_NAME}} < RegexLexer`)
- [lib/rouge/lexer/{{LANGUAGE_SHORT_NAME}}.rb](lib/rouge/lexer/{{LANGUAGE_SHORT_NAME}}.rb) — Entry point that `require`s the lexer; this is what consumers load
- [spec/rouge_lexer_{{LANGUAGE_SHORT_NAME}}_spec.rb](spec/rouge_lexer_{{LANGUAGE_SHORT_NAME}}_spec.rb) — Minitest test suite
- [spec/demos/{{LANGUAGE_SHORT_NAME}}](spec/demos/{{LANGUAGE_SHORT_NAME}}) — Short demo snippet used in tests and Rouge's demo pages
- [spec/visual/samples/{{LANGUAGE_SHORT_NAME}}](spec/visual/samples/{{LANGUAGE_SHORT_NAME}}) — Comprehensive sample used for visual testing and error-token checks

**Lexer structure:** The lexer uses class-level `Set` caches for keyword
classification. The `:root` state matches tokens in priority order; a catch-all
`\w+` rule does set-membership lookups to assign the correct token type. Sub-states
handle comments, strings, and any other paired constructs.

## {{LANGUAGE_NAME}} references

Use *ONLY* official documentation, *NOT* from memory, training, or inference.

### Documentation

**MANDATORY: Before writing or modifying the lexer, you MUST fetch and read every
URL in this list.** This is not background reading — it is a required prerequisite
step. Fetch each page, extract the keywords or function names, and verify them
against the lexer before declaring any work complete.

{{DOCUMENTATION_URLS}}

## Rouge references

- Lexer development guide: <https://github.com/rouge-ruby/rouge/blob/main/docs/LexerDevelopment.md>
- Existing lexers for reference: <https://github.com/rouge-ruby/rouge/tree/main/lib/rouge/lexers>
- JSON lexer (simple example): <https://github.com/rouge-ruby/rouge/blob/main/lib/rouge/lexers/json.rb>
- SQL lexer (keyword-heavy analog): <https://github.com/rouge-ruby/rouge/blob/main/lib/rouge/lexers/sql.rb>
- Token types: <https://github.com/rouge-ruby/rouge/blob/main/lib/rouge/token.rb>

## Verification workflow (MANDATORY — do this BEFORE adding anything)

Before writing or modifying the lexer, fetch **every URL in the documentation
list** above. Do not begin implementation until all pages have been read.

Before adding ANY individual keyword, function, or syntax element:

1. **Fetch the relevant documentation page** using the WebFetch tool or curl.
2. **Extract and confirm** the element exists in the fetched content. Do not rely
   on training data, memory, or assumptions about what "should" exist.
3. **Only add** elements that appear in the fetched content. **Only remove**
   elements confirmed absent.

### What NOT to do

- **Do NOT add keywords or syntax from training data or memory.** Every addition
  must be traced to a specific URL from the reference list in this file.
- **Do NOT use preview/beta features** unless explicitly asked. Only add GA
  (generally available) features.
- **Do NOT fabricate or modify reference URLs.** Use ONLY the exact URLs listed
  in this file. If a URL doesn't work, say so — do not guess an alternative.
- **Do NOT assume a function exists because a similar one does.**

### Self-verification

After making changes, verify correctness by **re-fetching the source documentation**
and confirming every added element appears in the fetched HTML. Do not verify by
re-reading your own changes.

### Constraints (applies to all work)

- **No hallucinated syntax.** Every keyword, function, operator, and language
  construct in the lexer must come from the official documentation listed above.
- **Follow Rouge conventions exactly.** Study existing lexers (especially JSON and
  SQL) for patterns. Don't invent novel approaches.
- **The Error token count is the ground truth.** The visual preview server is the
  authoritative test. `bundle exec rake` passing is necessary but not sufficient —
  you must also have zero `class="err"` spans.
- **Iterate until clean.** Do not declare the task complete until both
  `bundle exec rake` passes AND the Error token count is zero for both demo and
  visual sample.
- **Update the visual sample** (`spec/visual/samples/{{LANGUAGE_SHORT_NAME}}`)
  whenever new tokens are added to the lexer, so every token type has coverage.

The markdownlint configuration in [.vscode/settings.json](.vscode/settings.json)
sets `MD024` to `siblings_only: true`, allowing repeated heading text under
different parent headings (e.g. `### Added` appearing under multiple version
sections in the changelog).

## Changelog

The changelog ([CHANGELOG.md](CHANGELOG.md)) follows the
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/) format and
[Semantic Versioning](https://semver.org/spec/v2.0.0.html). When updating the
changelog:

- Use `## [version] - YYYY-MM-DD` for release headings
- Use `### Added`, `### Changed`, `### Removed` as second-level section headings
- Use `#### Category name` as optional third-level headings within a section
- Ensure blank lines surround all headings to satisfy markdownlint
```

---

## Final checklist

Before considering the project complete, verify:

- [ ] `bundle exec rake` passes with no test failures
- [ ] `curl -s http://localhost:9292 | grep 'class="err"'` returns nothing
- [ ] Every token type defined in the lexer has at least one example in the visual sample
- [ ] Every keyword/function in the lexer is traceable to a documentation URL
- [ ] `README.md`, `AGENTS.md`, `CLAUDE.md`, and `CHANGELOG.md` are accurate
- [ ] The gemspec version is `0.1.0` and metadata URLs are correct
