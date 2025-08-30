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
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/docs.yml@v1
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
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/docs.yml@v1
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
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/docs.yml@v1
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

If you have a `check_docs.py` script in your tools repo, it will be run automatically:

```python
# nt-espidf-tools/check_docs.py
import sys
import markdown
import re

def check_docs(markdown_file):
    """Check documentation links and structure"""
    # Your link checking logic here
    pass

if __name__ == "__main__":
    if len(sys.argv) > 1:
        check_docs(sys.argv[1])
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

