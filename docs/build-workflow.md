# Build Workflow Guide

The ESP-IDF Build workflow provides matrix-based building across multiple ESP-IDF versions, build types, and applications with intelligent caching and artifact management.

## 📋 Table of Contents

- [Overview](#overview)
- [Inputs](#inputs)
- [Outputs](#outputs)
- [Usage Examples](#usage-examples)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Navigation](#navigation)

## 🎯 Overview

**Purpose**: Matrix builds across ESP-IDF versions and build types  
**Key Features**: 
- Dynamic matrix generation from your project's configuration
- Intelligent caching (pip, ccache)
- Build artifact uploads
- ESP-IDF environment management via official actions

**Use Case**: CI/CD for ESP32 applications requiring multiple build configurations

## ⚙️ Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `project_dir` | string | ✅ | - | Path to ESP-IDF project (contains CMakeLists.txt) |
| `scripts_dir` | string | ❌ | `nt-espidf-tools` | Path to nt-espidf-tools directory |
| `build_path` | string | ❌ | `ci_build_path` | Staging/build workspace directory |
| `clean_build` | boolean | ❌ | `false` | Skip caches for clean build |
| `auto_clone_tools` | boolean | ❌ | `false` | Auto-clone tools repo if missing |
| `tools_repo_url` | string | ❌ | `https://github.com/N3b3x/nt-espidf-tools.git` | Git URL for tools repo |
| `tools_repo_ref` | string | ❌ | `main` | Branch/tag for tools repo |

## 📤 Outputs

| Output | Description |
|--------|-------------|
| `matrix` | JSON matrix from generate_matrix.py |
| Build artifacts | Uploaded for each matrix combination |

## 🚀 Usage Examples

### Basic Usage

```yaml
jobs:
  build:
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
      build_path: ci_build_path
```

### With Auto-clone Tools

```yaml
jobs:
  build:
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
      auto_clone_tools: true
      tools_repo_url: https://github.com/N3b3x/nt-espidf-tools.git
      tools_repo_ref: main
      build_path: ci_build_path
      clean_build: false
```

### Clean Build (No Caching)

```yaml
jobs:
  build:
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
      clean_build: true  # Skip all caches
```

## ⚙️ Configuration

### Matrix Generation

The workflow calls your project's `generate_matrix.py` script to create the build matrix:

```python
# Expected output format from generate_matrix.py
{
  "include": [
    {
      "idf_version": "release/v5.5",
      "idf_version_docker": "v5.5",
      "build_type": "debug",
      "target": "esp32c6",
      "app_name": "my_app"
    }
  ]
}
```

### Required Scripts

Your `nt-espidf-tools` directory must contain:

- `generate_matrix.py` - Matrix generator
- `setup_ci.sh` - CI workspace preparation
- `build_app.sh` - Application builder
- `requirements.txt` - Python dependencies (optional)

### Build Process

1. **Matrix Generation**: Runs `generate_matrix.py` to create build combinations
2. **Workspace Setup**: Calls `setup_ci.sh` to prepare build directory
3. **ESP-IDF Setup**: Uses `espressif/esp-idf-ci-action` for environment
4. **Build Execution**: Runs `build_app.sh` for each matrix combination
5. **Artifact Upload**: Uploads build outputs as artifacts

## 🔧 Troubleshooting

### Common Issues

**Matrix Generation Fails**
- Ensure `generate_matrix.py` exists in `scripts_dir`
- Check Python dependencies are installed
- Verify script outputs valid JSON

**Build Fails**
- Check `setup_ci.sh` creates proper workspace
- Verify `build_app.sh` handles all matrix parameters
- Ensure ESP-IDF version compatibility

**Cache Issues**
- Set `clean_build: true` to bypass caches
- Check cache key generation in workflow
- Verify cache paths are accessible

### Debug Mode

Enable debug output by setting:

```yaml
env:
  ACTIONS_STEP_DEBUG: true
  ACTIONS_RUNNER_DEBUG: true
```

## 📚 Related Workflows

- **[Lint](lint-workflow.md)** - Code quality checks
- **[Static Analysis](static-analysis-workflow.md)** - Security analysis
- **[Security](security-workflow.md)** - Security auditing

## 🔗 Related Resources

- [ESP-IDF CI Action](https://github.com/espressif/esp-idf-ci-action)
- [GitHub Actions Caching](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows)
- [Matrix Strategy](https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs)

---

**← Back to [Documentation Index](index.md)**  
**← Back to [Main README](../README.md)**

