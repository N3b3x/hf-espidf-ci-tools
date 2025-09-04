# Lint Workflow Guide

The C/C++ Lint workflow runs clang-format and clang-tidy using cpp-linter for code quality enforcement.

## 📋 Table of Contents

- [Overview](#overview)
- [Inputs](#inputs)
- [Outputs](#outputs)
- [Usage Examples](#usage-examples)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Navigation](#navigation)

## 🎯 Overview

**Purpose**: C/C++ code quality enforcement using cpp-linter-action  
**Key Features**: 
- clang-format for code style
- clang-tidy for static analysis
- PR annotations and comments
- Configurable file patterns
- **Auto-fix and push formatting changes**
- **Direct integration with cpp-linter/cpp-linter-action@v2**

**Use Case**: Code style consistency and quality enforcement

## ⚙️ Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `paths` | string | ❌ | `src/**,inc/**,examples/**` | Comma-separated globs to lint |
| `clang_version` | string | ❌ | `20` | Clang major version |
| `auto_fix` | boolean | ❌ | `false` | Automatically format files and push changes back to the repository |
| `commit` | boolean | ❌ | `true` | Automatically commit and push fixes to the repository (only used when auto_fix is true) |
| `commit_message` | string | ❌ | `"Auto-format code with clang-format"` | Commit message for auto-fix changes |
| `git_name` | string | ❌ | `"cpp-linter-action"` | Git author name for auto-fix commits |
| `git_email` | string | ❌ | `"cpp-linter-action@github.com"` | Git author email for auto-fix commits |
| `style` | string | ❌ | `"file"` | clang-format style (llvm, google, webkit, or 'file' to use .clang-format) |
| `tidy_checks` | string | ❌ | `""` | clang-tidy checks (comma-separated glob patterns, use - prefix to disable) |
| `ignore` | string | ❌ | `"build\|.git"` | Files or directories to exclude from linting (comma-separated) |
| `files_changed_only` | boolean | ❌ | `false` | Only lint files that have been changed in the pull request |
| `lines_changed_only` | boolean | ❌ | `false` | Only inspect lines that have changed in the pull request |
| `step_summary` | boolean | ❌ | `true` | Add a summary of linting results to the workflow step summary |
| `file_annotations` | boolean | ❌ | `true` | Display linting errors and warnings as file annotations in the GitHub UI |
| `thread_comments` | boolean | ❌ | `false` | Post a single, collapsible thread comment in a pull request detailing all linting issues |

## 📤 Outputs

| Output | Description |
|--------|-------------|
| PR annotations | File-level annotations for style issues |
| PR comments | Summary comments with issue counts |
| Style fixes | Automatic formatting suggestions |

## 🚀 Usage Examples

### Basic Usage

```yaml
jobs:
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      clang_version: "20"
```

### Custom Paths

```yaml
jobs:
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,lib/**,tests/**"
      clang_version: "18"
```

### Auto-Fix and Push Changes

```yaml
jobs:
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      clang_version: "20"
      auto_fix: true
      commit_message: "Auto-format code with clang-format-20"
```

### Auto-Fix on Specific Branches Only

```yaml
jobs:
  lint:
    if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/develop'
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      auto_fix: true
      commit_message: "Auto-format: $(date '+%Y-%m-%d %H:%M:%S')"
```

### Advanced Configuration with Custom Git Author

```yaml
jobs:
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      clang_version: "20"
      style: "google"  # Use Google style instead of .clang-format file
      tidy_checks: "readability-*,performance-*,modernize-*"  # Specific checks
      auto_fix: true
      commit: true
      commit_message: "Auto-format with clang-format-20 (Google style)"
      git_name: "ESP-IDF Bot"
      git_email: "bot@espidf-project.com"
      files_changed_only: true  # Only check changed files in PRs
      thread_comments: true     # Post collapsible thread comments
```

### Performance Optimized for Large Projects

```yaml
jobs:
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      clang_version: "20"
      files_changed_only: true   # Only lint changed files
      lines_changed_only: true   # Only check changed lines
      ignore: "build|.git|third_party|vendor"  # Exclude more directories
      step_summary: true         # Show summary in workflow
      file_annotations: true     # Show inline annotations
      thread_comments: false     # Disable thread comments for performance
```

### Disable Auto-Commit (Format Only)

```yaml
jobs:
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      auto_fix: true
      commit: false  # Format files but don't commit
      # Files will be formatted but changes won't be pushed
```

## 🔧 Auto-Fix Behavior

When `auto_fix: true` is enabled:

- **Automatic Formatting**: clang-format will automatically fix formatting issues
- **Commit and Push**: Changes are automatically committed and pushed back to the repository
- **Branch Safety**: Only works on branches where the workflow has write permissions
- **Commit Message**: Uses the provided `commit_message` or the default message
- **File Annotations**: Still provides PR annotations showing what was changed

### ⚠️ Important Notes

- **Permissions Required**: The workflow needs `contents: write` permission
- **Branch Protection**: Be careful with auto-fix on protected branches
- **Review Process**: Consider using auto-fix only on development branches
- **Backup**: Always ensure you have backups before enabling auto-fix

## ⚙️ Configuration

### .clang-format

Create a `.clang-format` file in your project root:

```yaml
BasedOnStyle: Google
IndentWidth: 2
ColumnLimit: 100
AccessModifierOffset: -2
```

### .clang-tidy

Create a `.clang-tidy` file for static analysis:

```yaml
Checks: '*,readability-*,performance-*,modernize-*'
WarningsAsErrors: ''
HeaderFilterRegex: ''
```

## 🔧 Troubleshooting

### Common Issues

**Style Issues Not Detected**
- Verify `.clang-format` exists and is valid
- Check file extensions are included
- Ensure paths match your project structure

**Clang Version Issues**
- Use supported versions (18, 19, 20)
- Check cpp-linter compatibility

## 📚 Related Workflows

- **[Build](build-workflow.md)** - ESP-IDF application builds
- **[Static Analysis](static-analysis-workflow.md)** - Security analysis
- **[Security](security-workflow.md)** - Security auditing

## 🔗 Related Resources

- [cpp-linter Action](https://github.com/cpp-linter/cpp-linter-action)
- [Clang Format](https://clang.llvm.org/docs/ClangFormat.html)
- [Clang Tidy](https://clang.llvm.org/extra/clang-tidy/)

---

**← Back to [Documentation Index](index.md)**  
**← Back to [Main README](../README.md)**

