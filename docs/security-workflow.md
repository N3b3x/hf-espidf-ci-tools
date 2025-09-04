# Security Workflow Guide

The Security workflow provides comprehensive security auditing including dependency scanning, secret detection, and optional CodeQL analysis.

## 📋 Table of Contents

- [Overview](#overview)
- [Inputs](#inputs)
- [Outputs](#outputs)
- [Usage Examples](#usage-examples)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Navigation](#navigation)

## 🎯 Overview

**Purpose**: Comprehensive security auditing  
**Key Features**: 
- Python dependency vulnerability scanning
- Secret and credential detection
- Optional CodeQL analysis
- Configurable scan types

**Use Case**: Security compliance and vulnerability detection

## ⚙️ Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `project_dir` | string | ✅ | - | Path to ESP-IDF project (contains CMakeLists.txt) |
| `scripts_dir` | string | ❌ | `nt-espidf-tools` | Path to nt-espidf-tools directory |
| `scan_type` | string | ❌ | `all` | `all \| dependencies \| secrets \| codeql` |
| `run_codeql` | boolean | ❌ | `true` | Enable CodeQL analysis |
| `codeql_languages` | string | ❌ | `cpp` | Comma-separated languages |
| `auto_clone_tools` | boolean | ❌ | `false` | Auto-clone tools repo if missing |
| `tools_repo_url` | string | ❌ | `https://github.com/N3b3x/nt-espidf-tools.git` | Git URL for tools repo |
| `tools_repo_ref` | string | ❌ | `main` | Branch/tag for tools repo |

## 📤 Outputs

| Output | Description |
|--------|-------------|
| Dependency reports | pip-audit, safety, bandit results |
| Secret detection | gitleaks, truffleHog reports |
| CodeQL analysis | Security database and SARIF files |
| Artifacts | All security reports stored |

## 🚀 Usage Examples

### Full Security Audit

```yaml
jobs:
  security:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
      scan_type: "all"
      run_codeql: true
      codeql_languages: "cpp,python"
```

### Dependencies Only

```yaml
jobs:
  security:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
      scan_type: "dependencies"
      run_codeql: false
```

### CodeQL with Auto-clone Tools

```yaml
jobs:
  security:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
      auto_clone_tools: true
      tools_repo_url: https://github.com/N3b3x/nt-espidf-tools.git
      tools_repo_ref: main
      scan_type: "codeql"
      run_codeql: true
      codeql_languages: "cpp"
```

## ⚙️ Configuration

### Scan Types

- **`all`**: Run all security checks
- **`dependencies`**: Python dependency scanning only
- **`secrets`**: Secret detection only  
- **`codeql`**: CodeQL analysis only

### CodeQL Languages

Supported languages: `cpp`, `python`, `javascript`, `go`, `java`, `csharp`

### Matrix-Based CodeQL Analysis

The workflow automatically generates the same build matrix as your build workflow and analyzes **all** build configurations:

1. **Same matrix generation**: Uses `generate_matrix.py` from your tools repo
2. **Same build process**: Calls `build_app.sh` with matrix parameters
3. **Comprehensive coverage**: Analyzes all ESP-IDF versions, build types, and targets
4. **Build consistency**: Each matrix job uses the exact same build configuration

**Matrix Parameters Used**:
- `matrix.app_name` - Application name
- `matrix.build_type` - Build type (debug/release)
- `matrix.idf_version` - ESP-IDF version
- `matrix.target` - Target chip (esp32, esp32c6, etc.)

**Example Integration**:
```yaml
# In your CI workflow, ensure consistency:
jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
      # ... other inputs

  security:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
      scan_type: "codeql"
      # CodeQL will analyze ALL matrix combinations automatically
```

## 🔧 Troubleshooting

### Common Issues

**Dependency Scan Fails**
- Check Python version compatibility
- Verify requirements files exist
- Review pip installation logs

**Secret Detection Issues**
- Ensure full git history is available
- Check gitleaks installation
- Review false positive suppressions

**CodeQL Analysis Fails**
- Verify `project_dir` and `scripts_dir` are correct
- Check `build_app.sh` exists in scripts directory
- Ensure matrix generation works (same as build workflow)
- Check ESP-IDF version compatibility

### Debug Mode

Enable debug output:

```yaml
env:
  ACTIONS_STEP_DEBUG: true
  ACTIONS_RUNNER_DEBUG: true
```

## 📚 Related Workflows

- **[Build](build-workflow.md)** - ESP-IDF application builds (uses same matrix and build process)
- **[Static Analysis](static-analysis-workflow.md)** - Security analysis
- **[Lint](lint-workflow.md)** - Code quality checks

## 🔗 Related Resources

- [CodeQL Documentation](https://codeql.github.com/)
- [GitLeaks](https://github.com/gitleaks/gitleaks)
- [pip-audit](https://github.com/pypa/pip-audit)
- [Safety](https://github.com/pyupio/safety)

---

**← Back to [Documentation Index](index.md)**  
**← Back to [Main README](../README.md)**

