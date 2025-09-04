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
- GitHub Pages deployment
- Artifact storage

**Use Case**: Automated documentation generation and deployment for ESP-IDF projects

## ⚙️ Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `project_dir` | string | ✅ | - | Path containing Doxyfile and sources |
| `scripts_dir` | string | ❌ | `nt-espidf-tools` | Path to nt-espidf-tools directory |
| `doxygen_config` | string | ❌ | `Doxyfile` | Path to Doxyfile (relative to repo root) |
| `output_dir` | string | ❌ | `docs/doxygen/html` | Generated HTML directory |
| `run_link_check` | boolean | ❌ | `true` | Run docs/check_docs.py if present |
| `auto_clone_tools` | boolean | ❌ | `false` | Auto-clone tools repo if missing |
| `tools_repo_url` | string | ❌ | `https://github.com/N3b3x/nt-espidf-tools.git` | Git URL for tools repo |
| `tools_repo_ref` | string | ❌ | `main` | Branch/tag for tools repo |
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
      project_dir: .
      scripts_dir: nt-espidf-tools
      doxygen_config: Doxyfile
      output_dir: docs/doxygen/html
```

### With Auto-clone Tools

```yaml
jobs:
  docs:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/docs.yml@v1
    with:
      project_dir: .
      scripts_dir: nt-espidf-tools
      auto_clone_tools: true
      tools_repo_url: https://github.com/N3b3x/nt-espidf-tools.git
      tools_repo_ref: main
      doxygen_config: Doxyfile
      output_dir: docs/doxygen/html
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
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
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
- Supports both built-in checker and external `md-dead-link-check` tool

**No external dependencies required** - the link checker is built into the workflow and doesn't require project tools.

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

**GitHub Pages Not Deploying**
- Check repository permissions
- Verify `deploy_pages: true`
- Ensure workflow runs on main branch (not PRs)

**Link Check Fails**
- Verify `check_docs.py` exists in tools repo
- Check script handles errors gracefully
- Review script output for specific issues

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

