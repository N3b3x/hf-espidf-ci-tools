# Documentation Workflow Guide

The Documentation workflow builds Doxygen documentation and optionally deploys it to GitHub Pages with link checking and artifact management.

## 📋 Table of Contents

- [Overview](#overview)
- [Inputs](#inputs)
- [Outputs](#outputs)
- [Usage Examples](#usage-examples)
- [Configuration](#configuration)
- [GitHub Pages Setup](#github-pages-setup)
- [Troubleshooting](#troubleshooting)
- [Navigation](#navigation)

## 🎯 Overview

**Purpose**: Generate and deploy Doxygen documentation  
**Key Features**: 
- Doxygen + Graphviz integration
- Optional link checking
- Markdown linting (markdownlint)
- Spell checking (cspell)
- GitHub Pages deployment
- Artifact storage

**Use Case**: Automated documentation generation and deployment for ESP-IDF projects

## ⚙️ Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `doxygen_config` | string | ❌ | `Doxyfile` | Path to Doxyfile (relative to repo root) |
| `output_dir` | string | ❌ | `docs/doxygen/html` | Generated HTML directory |
| `run_link_check` | boolean | ❌ | `true` | Run documentation link checker |
| `link_check_paths` | string | ❌ | `docs/**,*.md,**/docs/**` | Comma-separated paths to check for broken links |
| `run_markdown_lint` | boolean | ❌ | `false` | Run markdown linting on documentation files |
| `markdown_lint_paths` | string | ❌ | `**/*.md` | Glob patterns for markdown files to lint |
| `run_spell_check` | boolean | ❌ | `false` | Run spell checking on documentation files |
| `spell_check_paths` | string | ❌ | `**/*.md` | Glob patterns for files to spell check |
| `spell_check_config` | string | ❌ | `.cspell.json` | Path to cspell configuration file |
| `deploy_pages` | boolean | ❌ | `true` | Deploy to GitHub Pages |

## 📤 Outputs

| Output | Description |
|--------|-------------|
| HTML documentation | Generated Doxygen HTML files |
| GitHub Pages | Deployed documentation (if enabled) |
| Artifacts | Uploaded documentation files |

## 🚀 Usage Examples

### Basic Usage

```yaml
jobs:
  docs:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/docs.yml@v1
    with:
      doxygen_config: Doxyfile
      output_dir: docs/doxygen/html
```

### With Documentation Quality Checks

```yaml
jobs:
  docs:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/docs.yml@v1
    with:
      doxygen_config: Doxyfile
      output_dir: docs/doxygen/html
      run_link_check: true
      link_check_paths: "docs/**,*.md,**/docs/**,examples/**"
      run_markdown_lint: true
      markdown_lint_paths: "docs/**,*.md"
      run_spell_check: true
      spell_check_paths: "docs/**,*.md"
      spell_check_config: ".cspell.json"
      deploy_pages: true
    secrets:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Custom Configuration

```yaml
jobs:
  docs:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/docs.yml@v1
    with:
      doxygen_config: docs/Doxyfile.custom
      output_dir: docs/generated/html
      run_link_check: false
      deploy_pages: false
```

## ⚙️ Configuration

### Doxygen Configuration

Create a `Doxyfile` in your project root:

```ini
# Basic Doxygen configuration
PROJECT_NAME           = "My ESP32 Project"
PROJECT_NUMBER        = 1.0
OUTPUT_DIRECTORY      = docs/doxygen
INPUT                 = src inc examples
FILE_PATTERNS         = *.c *.cpp *.h *.hpp
RECURSIVE             = YES
GENERATE_HTML         = YES
HTML_OUTPUT           = html
GENERATE_LATEX        = NO
EXTRACT_ALL           = YES
EXTRACT_PRIVATE       = YES
EXTRACT_STATIC        = YES
```

### Link Checking

The workflow includes a built-in link checker that verifies all local links in documentation files are valid. This helps maintain documentation quality and prevents broken links.

```yaml
run_link_check: true  # Enable link checking (default: true)
link_check_paths: "docs/**,*.md"  # Paths to check for broken links
```

The link checker:
- Scans all markdown files in the specified paths
- Validates local file references and cross-directory links
- Ignores external URLs, anchors, and common non-file patterns
- Reports broken links with file and line number information
- Can be configured to fail the workflow on broken links
- Uses the external `md-dead-link-check` action directly

### Markdown Linting

Optional markdown linting using `markdownlint-cli` to enforce consistent markdown formatting and catch common issues.

```yaml
run_markdown_lint: true  # Enable markdown linting (default: false)
markdown_lint_paths: "**/*.md"  # Glob patterns for files to lint
```

Features:
- Enforces consistent markdown formatting
- Catches common markdown issues
- Configurable via `.markdownlint.json` file
- Ignores `node_modules` by default

### Spell Checking

Optional spell checking using `cspell` to catch spelling errors in documentation.

```yaml
run_spell_check: true  # Enable spell checking (default: false)
spell_check_paths: "**/*.md"  # Glob patterns for files to check
spell_check_config: ".cspell.json"  # Path to cspell config file
```

Features:
- Catches spelling errors in documentation
- Configurable via `.cspell.json` file
- Supports custom dictionaries and ignore patterns
- Works with multiple file types

### Link Checker Options

**Link Checking (md-dead-link-check):**
```yaml
run_link_check: true
link_check_paths: "docs/**,*.md,**/docs/**"
# Uses external md-dead-link-check action directly
# The external action uses its default configuration
```

### Standalone Link Check

For repositories that only need link checking without documentation generation, use the dedicated link check workflow:

```yaml
jobs:
  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "docs/**,*.md,**/docs/**"  # Paths to check
      fail_on_errors: true              # Fail on broken links
      # Uses external md-dead-link-check action directly
```

### GitHub Pages Setup

1. **Enable GitHub Pages** in your repository settings
2. **Set source** to "GitHub Actions"
3. **Grant permissions** to the workflow:

```yaml
permissions:
  contents: write
  pages: write
  id-token: write
```

## 🌐 GitHub Pages Setup

### Repository Settings

1. Go to **Settings** → **Pages**
2. Set **Source** to "GitHub Actions"
3. Ensure **GitHub Actions** has write permissions

### Workflow Permissions

The workflow requires these permissions:

```yaml
permissions:
  contents: write      # Upload artifacts
  pages: write         # Deploy to Pages
  id-token: write      # Authentication
```

### Custom Domain (Optional)

Add a `CNAME` file to your `docs/doxygen/html` directory:

```
docs.yourproject.com
```

## 🔧 Troubleshooting

### Common Issues

**Doxygen Build Fails**
- Verify `Doxyfile` exists and is valid
- Check source directories exist (`src/`, `inc/`, `examples/`)
- Ensure Graphviz is installed (handled automatically)
- Verify `doxygen_config` path is correct

**GitHub Pages Not Deploying**
- Check repository permissions
- Verify `deploy_pages: true`
- Ensure workflow runs on main branch (not PRs)

**Link Check Fails**
- Verify link paths are correct in `link_check_paths`
- Check that markdown files exist at specified paths
- Review link checker output for specific broken links

**Markdown Lint Fails**
- Check markdown syntax and formatting
- Review `.markdownlint.json` configuration if present
- Fix linting errors reported by markdownlint

**Spell Check Fails**
- Review spelling errors in documentation
- Add words to `.cspell.json` custom dictionary if needed
- Check `spell_check_config` path is correct

### Debug Mode

Enable debug output:

```yaml
env:
  ACTIONS_STEP_DEBUG: true
```

### Manual Testing

Test Doxygen locally:

```bash
# Install Doxygen and Graphviz
sudo apt-get install doxygen graphviz

# Generate docs
doxygen Doxyfile

# Check output
ls docs/doxygen/html/
```

## 📚 Related Workflows

- **[Build](build-workflow.md)** - ESP-IDF application builds
- **[Lint](lint-workflow.md)** - Code quality checks
- **[Security](security-workflow.md)** - Security auditing

## 🔗 Related Resources

- [Doxygen Documentation](https://www.doxygen.nl/)
- [GitHub Pages](https://docs.github.com/en/pages)
- [Markdown Link Checking](https://github.com/tcort/markdown-link-check)

---

**← Back to [Documentation Index](index.md)**  
**← Back to [Main README](../README.md)**

