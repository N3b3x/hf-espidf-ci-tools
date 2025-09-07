# Lint Workflow Guide

<div align="center">

[← Previous: Docs Workflow](docs-workflow.md) | [Next: Link Check Workflow →](link-check-workflow.md)

**🔍 C/C++ Code Quality Checks**

</div>

---

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
- Configurable file extensions
- Configurable exclude paths
- **Direct integration with cpp-linter/cpp-linter-action@v2**

**Use Case**: Code style consistency and quality enforcement

## ⚙️ Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `clang_version` | string | ❌ | `20` | Clang major version |
| `style` | string | ❌ | `"file"` | clang-format style (llvm, google, webkit, or 'file' to use .clang-format) |
| `tidy_checks` | string | ❌ | `""` | clang-tidy checks (comma-separated glob patterns, use - prefix to disable) |
| `extensions` | string | ❌ | `"c,cpp,cc,cxx,h,hpp"` | File extensions to check (comma-separated) |
| `ignore` | string | ❌ | `"build\|.git"` | Files or directories to exclude from linting (pipe-separated) |
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
| Step summary | Workflow step summary with linting results |

## 🚀 Usage Examples

### Basic Usage

```yaml
jobs:
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      clang_version: "20"
```

### Custom File Extensions

```yaml
jobs:
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      extensions: "c,cpp,h,hpp,cc,cxx"
      clang_version: "18"
```

### Custom Exclude Paths

```yaml
jobs:
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      ignore: "build|.git|third_party|vendor|test"
      clang_version: "20"
```

### Advanced Configuration

```yaml
jobs:
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      clang_version: "20"
      style: "google"  # Use Google style instead of .clang-format file
      tidy_checks: "readability-*,performance-*,modernize-*"  # Specific checks
      extensions: "c,cpp,h,hpp"
      ignore: "build|.git|third_party"
      files_changed_only: true  # Only check changed files in PRs
      thread_comments: true     # Post collapsible thread comments
```

### Performance Optimized for Large Projects

```yaml
jobs:
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      clang_version: "20"
      files_changed_only: true   # Only lint changed files
      lines_changed_only: true   # Only check changed lines
      ignore: "build|.git|third_party|vendor"  # Exclude more directories
      step_summary: true         # Show summary in workflow
      file_annotations: true     # Show inline annotations
      thread_comments: false     # Disable thread comments for performance
```

## 🔧 Linting Behavior

The workflow provides:

- **Code Style Checking**: clang-format checks for style violations
- **Static Analysis**: clang-tidy performs static analysis checks
- **PR Feedback**: Annotations and comments on pull requests
- **Configurable Scope**: Control which files and paths are checked
- **Performance Options**: Check only changed files/lines for faster runs
- **File Annotations**: Provides PR annotations for linting issues

### ⚠️ Important Notes

- **Permissions Required**: The workflow needs `pull-requests: write` permission for PR comments
- **File Discovery**: The action automatically discovers C/C++ files in your repository
- **Configuration Files**: Uses `.clang-format` and `.clang-tidy` files if present

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

<div align="center">

[← Previous: Docs Workflow](docs-workflow.md) | [Next: Link Check Workflow →](link-check-workflow.md)

**📚 [All Documentation](index.md)** | **🏠 [Main README](../README.md)**

</div>

