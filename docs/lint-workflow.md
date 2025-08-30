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

**Purpose**: C/C++ code quality enforcement  
**Key Features**: 
- clang-format for code style
- clang-tidy for static analysis
- PR annotations and comments
- Configurable file patterns

**Use Case**: Code style consistency and quality enforcement

## ⚙️ Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `paths` | string | ❌ | `src/**,inc/**,examples/**` | Comma-separated globs to lint |
| `clang_version` | string | ❌ | `20` | Clang major version |

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
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      clang_version: "20"
```

### Custom Paths

```yaml
jobs:
  lint:
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,lib/**,tests/**"
      clang_version: "18"
```

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

