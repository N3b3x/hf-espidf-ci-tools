# 🏗️ ESP-IDF Build Workflow Guide

<div align="center">

![Build](https://img.shields.io/badge/Build-Matrix%20ESP--IDF-blue?style=for-the-badge&logo=espressif)
![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-orange?style=for-the-badge&logo=github)
![Matrix](https://img.shields.io/badge/Matrix-Parallel%20Builds-green?style=for-the-badge&logo=matrix)

**🚀 Advanced ESP32 Build System for HardFOC Projects**

*Matrix-based building across multiple ESP-IDF versions, build types, and applications with intelligent caching and artifact management*

</div>

<div align="center">

[← Previous: Documentation Index](index.md) | [Next: Docs Workflow →](docs-workflow.md)

</div>

---

## 📋 Table of Contents

- [🎯 Overview](#-overview)
- [⚙️ Inputs & Configuration](#️-inputs--configuration)
- [📤 Outputs & Artifacts](#-outputs--artifacts)
- [🚀 Usage Examples](#-usage-examples)
- [🔧 Configuration](#-configuration)
- [🏗️ Build Process](#️-build-process)
- [🔍 Troubleshooting](#-troubleshooting)
- [📚 Related Documentation](#-related-documentation)

---

## 🎯 Overview

### **Purpose**
The ESP-IDF Build workflow provides **matrix-based building** across multiple ESP-IDF versions, build types, and applications with intelligent caching and artifact management for HardFOC ESP32 projects.

### **Key Features** 
- 🔄 **Dynamic Matrix Generation** - From your project's `app_config.yml` configuration
- 🚀 **Parallel Execution** - Multiple builds run simultaneously for maximum efficiency
- 💾 **Intelligent Caching** - pip and ccache optimization for faster builds
- 📦 **Artifact Management** - Automatic upload and organization of build outputs
- 🛡️ **ESP-IDF Environment** - Official Espressif actions for reliable builds
- 📊 **Size Reporting** - Automatic PR comments with firmware size analysis
- 🔧 **Project Tools Integration** - Seamless integration with HardFOC project tools

### **Use Cases**
- ✅ **CI/CD for ESP32 applications** requiring multiple build configurations
- ✅ **Multi-version testing** across different ESP-IDF releases
- ✅ **Parallel builds** for faster development cycles
- ✅ **Automated artifact management** for releases and testing
- ✅ **Size optimization tracking** with PR integration

---

## ⚙️ Inputs & Configuration

### **Required Inputs**

| Input | Type | Required | Description |
|-------|------|----------|-------------|
| `project_dir` | string | ✅ | Path to ESP-IDF project (contains `CMakeLists.txt`, `app_config.yml`) |

### **Optional Inputs**

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `project_tools_dir` | string | Auto-detect | Path to project tools directory (contains scripts) |
| `clean_build` | boolean | `false` | Skip caches for a clean build |
| `auto_clone_tools` | boolean | `true` | Auto-clone tools repo if missing |

### **Path Configuration**

**Important:** All paths are resolved relative to the repository root where the workflow runs.

#### **Project Directory (`project_dir`)**
- **Purpose**: Points to your ESP-IDF project directory (contains `CMakeLists.txt`, `app_config.yml`)
- **Examples**: 
  - `examples/esp32` (most common)
  - `firmware/esp32-project`
  - `src/esp32-app`

#### **Tools Directory (`project_tools_dir`)**
- **Purpose**: Points to directory containing the build scripts (`build_app.sh`, `generate_matrix.py`, etc.)
- **Auto-detection**: If not specified, looks for `hf-espidf-project-tools` subdirectory in project directory
- **Examples**:
  - `examples/esp32/scripts` (when using submodule)
  - `examples/esp32/hf-espidf-project-tools` (when cloned directly)
  - `tools/esp32-build-scripts` (custom location)

#### **Common Configuration Patterns**

**Pattern 1: Submodule Setup (Recommended)**
```yaml
# Repository structure:
# your-repo/
# ├── examples/esp32/           # ESP-IDF project
# │   ├── CMakeLists.txt
# │   ├── app_config.yml
# │   └── hf-espidf-project-tools/  # Submodule containing project tools
# │       ├── build_app.sh
# │       └── generate_matrix.py

jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      project_tools_dir: examples/esp32/scripts  # Points to submodule
      # auto_clone_tools not needed - workflow respects explicit project_tools_dir
```

**✅ Automatic Behavior:**
- When `project_tools_dir` is specified, the workflow uses that path directly
- No auto-cloning occurs when explicit path is provided
- Submodule detection and validation happens automatically

**Pattern 2: Direct Clone Setup**
```yaml
# Repository structure:
# your-repo/
# ├── examples/esp32/           # ESP-IDF project
# │   ├── CMakeLists.txt
# │   ├── app_config.yml
# │   └── hf-espidf-project-tools/  # Cloned directly
# │       ├── build_app.sh
# │       └── generate_matrix.py

jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      # project_tools_dir not needed - auto-detects hf-espidf-project-tools
```

**Pattern 3: Custom Tools Location**
```yaml
# Repository structure:
# your-repo/
# ├── firmware/esp32/           # ESP-IDF project
# │   ├── CMakeLists.txt
# │   └── app_config.yml
# └── build-tools/              # Custom tools location
#     ├── build_app.sh
#     └── generate_matrix.py

jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: firmware/esp32
      project_tools_dir: build-tools
```

---

## 🔧 Troubleshooting

### **Common Issues**

#### **"build_app.sh: No such file or directory"**

**Cause**: Path resolution or submodule checkout issues

**Solutions**:
1. **Check Submodule Status**: Verify submodule is properly initialized
   ```bash
   git submodule status
   git submodule update --init --recursive
   ```
2. **Verify Paths**: Check that `project_tools_dir` points to the correct location
3. **Check Repository Structure**: Ensure the tools directory contains required scripts
4. **For Explicit Paths**: When `project_tools_dir` is specified, the workflow uses that path directly (no auto-cloning)

#### **"Project tools directory not found"**

**Cause**: Incorrect path configuration or missing tools directory

**Solutions**:
1. **Verify Paths**: Ensure paths are relative to repository root
2. **Check Auto-clone**: Set `auto_clone_tools: true` if not using submodules
3. **Manual Setup**: Clone tools repository manually if needed

#### **Submodule Detection Issues**

**Symptoms**: Workflow runs but can't find scripts in submodule

**Solutions**:
1. **Specify `project_tools_dir`** - When using submodules, explicitly set the path to the submodule
2. **Verify Submodule URL**: Ensure submodule points to correct repository
3. **Check Submodule Commit**: Ensure submodule is at a valid commit
4. **Auto-clone Logic**: Auto-clone only happens when `project_tools_dir` is not specified

### **Debug Information**

The workflow provides detailed logging to help diagnose issues:
- Path resolution details
- Submodule detection and validation
- Available directory listings
- Script validation results

---

## 📤 Outputs & Artifacts

### **Workflow Outputs**

| Output | Description |
|--------|-------------|
| `matrix` | JSON matrix from `generate_matrix.py` with all build combinations |
| `project_tools_path` | Resolved path to project tools directory |

### **Build Artifacts**

| Artifact | Description | Retention | Location |
|----------|-------------|-----------|----------|
| `fw-{app}-{version}-{build}` | Complete build directory with all outputs | 7 days | Root artifact |
| `size.txt` | Human-readable firmware size analysis | 7 days | Inside build directory |
| `size.json` | Machine-readable size data (JSON format) | 7 days | Inside build directory |
| `size.info` | Build metadata for PR reporting | 7 days | Inside build directory |
| `size.meta` | Additional metadata (ELF/MAP file paths) | 7 days | Inside build directory |
| `*.bin` | Firmware binary files | 7 days | Inside build directory |
| `*.elf` | ELF executable files | 7 days | Inside build directory |

**Artifact Structure:**
```
fw-gpio_test-release_v5_5-Release/
└── build-app-gpio_test-type-Release-target-esp32c6-idf-release_v5_5/  # ESP-IDF build directory
    ├── size.txt                      # Size analysis
    ├── size.json                     # JSON size data
    ├── size.info                     # PR metadata
    ├── size.meta                     # Additional metadata
    ├── gpio_test.bin                 # Firmware binary
    ├── gpio_test.elf                 # ELF file
    └── ...                           # Other build outputs
```

**Build Directory Naming:**
The build directory follows the pattern: `build-app-{app_type}-type-{build_type}-target-{target}-idf-{idf_version}`

Examples:
- `build-app-gpio_test-type-Release-target-esp32c6-idf-release_v5_5`
- `build-app-adc_test-type-Debug-target-esp32c6-idf-release_v5_4`

### **PR Integration**

- 📊 **Automatic Size Reports** - PR comments with firmware size analysis
- 📈 **Size Budget Enforcement** - Optional size limit checking
- 🔍 **Build Status** - Clear success/failure indicators

---

## 🚀 Usage Examples

### **Basic Usage**

```yaml
name: Build ESP32 Firmware

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
```

### **Advanced Configuration**

```yaml
name: Advanced ESP32 Build

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:
    inputs:
      clean_build:
        description: 'Clean build (no cache)'
        required: false
        default: false
        type: boolean

jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      project_tools_dir: hf-espidf-project-tools
      clean_build: ${{ github.event.inputs.clean_build == 'true' }}
      auto_clone_tools: true
```

### **Clean Build (No Caching)**

```yaml
jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      clean_build: true  # Skip all caches for fresh build
```

### **Custom Tools Directory**

```yaml
jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      project_tools_dir: custom-tools  # Use custom tools directory
      auto_clone_tools: false  # Don't auto-clone, use existing
```

---

## 🔧 Configuration

### **Project Structure Requirements**

Your ESP32 project must have this structure:

```
your-project/
├── examples/
│   └── esp32/                    # ESP-IDF project directory
│       ├── CMakeLists.txt        # ESP-IDF project file
│       ├── app_config.yml        # Build configuration
│       ├── main/                 # Application source code
│       ├── components/           # Custom components
│       └── hf-espidf-project-tools/  # Project tools (submodule)
│           ├── generate_matrix.py    # Matrix generator
│           ├── build_app.sh         # Application builder
│           ├── config_loader.sh     # Configuration loader
│           └── requirements.txt     # Python dependencies
```

### **Matrix Generation**

The workflow calls your project's `generate_matrix.py` script to create the build matrix:

```python
# Expected output format from generate_matrix.py
{
  "include": [
    {
      "idf_version": "release/v5.5",        # Git format for ESP-IDF cloning
      "idf_version_docker": "v5.5",         # Docker-safe format for artifacts
      "idf_version_file": "release_v5_5",   # File-safe format for build dirs
      "build_type": "Debug",                # Build configuration
      "app_name": "gpio_test",              # Application name
      "target": "esp32c6",                  # Target MCU
      "config_source": "app"                # Configuration source
    }
  ]
}
```

### **Required Scripts**

Your `hf-espidf-project-tools` directory must contain:

| Script | Purpose | Required |
|--------|---------|----------|
| `generate_matrix.py` | Matrix generator from `app_config.yml` | ✅ |
| `build_app.sh` | Application builder | ✅ |
| `config_loader.sh` | Configuration loader | ✅ |
| `requirements.txt` | Python dependencies | ❌ |

---

## 🏗️ Build Process

### **Phase 1: Matrix Generation**
```
┌─────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ Checkout    │───▶│ Ensure Tools    │───▶│ Install Python  │───▶│ Run generate    │───▶│ Output JSON     │
│ Repo        │    │ Directory       │    │ Deps            │    │ matrix.py       │    │ Matrix          │
└─────────────┘    └─────────────────┘    └─────────────────┘    └─────────────────┘    └─────────────────┘
```

### **Phase 2: Parallel Build Execution**
```
┌─────────────┐
│ Matrix      │
│ Entry       │
└──────┬──────┘
       │
       ▼
┌───────────────────────────────────────────────────────────────────────────────────────┐     
│  ┌─────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐ │
│  │ Checkout    │───▶│ Cache Python    │───▶│ Install Python  │───▶│ ESP-IDF CI      │ │
│  │ Repo        │    │ Deps            │    │ Deps            │    │ Action          │ │
│  └─────────────┘    └─────────────────┘    └─────────────────┘    └─────────────────┘ │
│                                                                            │          │
│                                                                            ▼          │
│  ┌─────────────┐    ┌─────────────────┐                           ┌─────────────────┐ │
│  │ Upload      │◀───│ Build with      │◀───---------------------──│ Cache ccache    │ │
│  │ Artifacts   │    │ build_app.sh    │ *ESP-IDF CI HAS IDF RDY*  │                 │ │
│  └─────────────┘    └─────────────────┘                           └─────────────────┘ │
└──────┬────────────────────────────────────────────────────────────────────────────────┘
       │
       ▼
┌─────────────┐
│ Matrix      │
│ Exit        │
└─────────────┘
```

### **Phase 3: Size Reporting**
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ Download        │───▶│ Parse Size      │───▶│ Generate        │───▶│ Comment on      │
│ Artifacts       │    │ Data            │    │ Markdown        │    │ PR              │
└─────────────────┘    └─────────────────┘    └─────────────────┘    └─────────────────┘
```

### **Detailed Build Steps**

1. **🔍 Matrix Generation**:
   - Validates project tools directory
   - Runs `generate_matrix.py` with project configuration
   - Outputs JSON matrix for parallel execution

2. **🏗️ Direct Project Building**:
   - ESP-IDF CI action works directly with project files
   - No file copying or workspace preparation needed
   - Builds directly from source directory structure

3. **🛡️ ESP-IDF Environment**:
   - Uses `espressif/esp-idf-ci-action@v1` for reliable environment
   - Sets up ESP-IDF tools and dependencies
   - Configures target and build parameters

4. **⚡ Build Execution**:
   - Runs `build_app.sh` with matrix parameters
   - Handles incremental builds with ccache
   - Generates size analysis and metadata

5. **📦 Artifact Management**:
   - Uploads firmware binaries and build outputs
   - Organizes artifacts by app, version, and build type
   - Sets appropriate retention policies

6. **📊 Size Reporting**:
   - Aggregates size data from all builds
   - Creates comprehensive PR comments
   - Enforces size budgets (optional)

---

## 🔍 Troubleshooting

### **Common Issues & Solutions**

#### **Matrix Generation Fails**
**Symptoms**: "Matrix generation failed" or "No matrix entries found"
**Solutions**:
```bash
# Check script exists and is executable
ls -la hf-espidf-project-tools/generate_matrix.py
chmod +x hf-espidf-project-tools/generate_matrix.py

# Test matrix generation locally
cd examples/esp32
python3 hf-espidf-project-tools/generate_matrix.py --validate

# Check Python dependencies
pip install pyyaml jq
```

#### **Build Fails**
**Symptoms**: "Build failed" or "ESP-IDF not found"
**Solutions**:
```bash
# Verify project structure is ready for building
ls -la src/ inc/ examples/
ls -la CMakeLists.txt app_config.yml

# Check build_app.sh handles matrix parameters
./hf-espidf-project-tools/build_app.sh --help

# Validate ESP-IDF version compatibility
python3 hf-espidf-project-tools/generate_matrix.py --validate
```

#### **Cache Issues**
**Symptoms**: "Cache miss" or "Build too slow"
**Solutions**:
```yaml
# Use clean build to bypass caches
clean_build: true

# Check cache configuration
# Cache keys should include relevant file hashes
```

#### **Artifact Upload Fails**
**Symptoms**: "Artifact upload failed" or "No artifacts found"
**Solutions**:
```bash
# Check build outputs exist
ls -la build-app-*/

# Verify build_app.sh generates proper outputs
./hf-espidf-project-tools/build_app.sh gpio_test Release

# Check size files are created inside build directory
ls -la build-app-*/size.*

# Verify artifact structure
find build-app-*/ -name "*.bin" -o -name "*.elf" -o -name "size.*"
```

#### **Size Report Issues**
**Symptoms**: "No size artifacts found" or "Size data missing"
**Solutions**:
```bash
# Check size files are created in build directory
ls -la build-app-*/size.*

# Verify size.info contains metadata
cat build-app-*/size.info

# Check build_app.sh size generation
./hf-espidf-project-tools/build_app.sh gpio_test Release
ls -la build-app-*/size.*
```

### **Debug Mode**

Enable verbose output for debugging:

```yaml
jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      clean_build: true  # Bypass caches for debugging
```

### **Local Testing**

Test the build process locally:

```bash
# 1. Setup environment
cd examples/esp32
source hf-espidf-project-tools/setup_common.sh

# 2. Test matrix generation
python3 hf-espidf-project-tools/generate_matrix.py --validate

# 3. Test direct building (no setup needed)

# 4. Test build
./hf-espidf-project-tools/build_app.sh gpio_test Release
```

---

## 📚 Related Documentation

- 📖 [**Main Documentation**](../README.md) - Complete CI tools overview
- 🔧 [**Lint Workflow**](lint-workflow.md) - C/C++ linting and formatting
- 📚 [**Docs Workflow**](docs-workflow.md) - Documentation building and deployment
- 🛡️ [**Security Workflow**](security-workflow.md) - Security auditing and analysis
- 🔍 [**Static Analysis**](static-analysis-workflow.md) - Code quality analysis
- 🔗 [**Link Checker**](link-check-workflow.md) - Documentation link validation

### **Project Tools Documentation**

- 🏗️ [**Build System**](../hf-espidf-project-tools/docs/README_BUILD_SYSTEM.md) - Complete build system guide
- ⚙️ [**Configuration System**](../hf-espidf-project-tools/docs/README_CONFIG_SYSTEM.md) - Configuration management
- 🚀 [**CI Pipeline**](../hf-espidf-project-tools/docs/README_CI_PIPELINE.md) - CI/CD integration guide
- 🔧 [**Scripts Overview**](../hf-espidf-project-tools/docs/README_SCRIPTS_OVERVIEW.md) - All available scripts
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

<div align="center">

[← Previous: Documentation Index](index.md) | [Next: Docs Workflow →](docs-workflow.md)

**📚 [All Documentation](index.md)** | **🏠 [Main README](../README.md)**

</div>

