---
layout: default
title: "📚 Documentation"
description: "Complete documentation navigation and setup guide for HardFOC ESP32 CI Tools"
nav_order: 1
has_children: true
permalink: /docs/
---

# Documentation Index

**📋 Documentation Index and Navigation**

---

Welcome to the hf-espidf-ci-tools documentation! This guide provides comprehensive coverage of all reusable workflows and how to integrate them into your ESP-IDF projects.

## 🚀 Quick Navigation

| Workflow | Description | Quick Start |
|----------|-------------|-------------|
| **[Build](build-workflow.md)** | ESP-IDF matrix builds with caching | [→ Build Guide](build-workflow.md) |
| **[Security](security-workflow.md)** | Dependencies, secrets, CodeQL | [→ Security Guide](security-workflow.md) |
| **[Release](release-workflow.md)** | Firmware releases with build artifacts | [→ Release Guide](release-workflow.md) |

## 📋 Prerequisites

Before using these workflows, ensure you have:

1. **ESP-IDF project** with proper structure
2. **hf-espidf-project-tools repository** cloned in your project
3. **GitHub Actions enabled** in your repository

## 🏗️ Project Structure

```
your-esp32-project/
├── .github/workflows/          # Your CI workflows
├── examples/esp32/             # ESP-IDF project (project_dir)
├── hf-espidf-project-tools/    # Project tools repo (project_tools_dir)
│   ├── generate_matrix.py      # Build matrix generator
│   ├── build_app.sh           # Application builder
│   ├── requirements.txt        # Python dependencies
│   └── config_loader.sh        # Configuration management
├── src/                        # Source code
├── inc/                        # Headers
└── CMakeLists.txt              # ESP-IDF project file
```

## 🔧 Basic Setup

### 1. Clone the Tools Repository

```bash
git clone https://github.com/N3b3x/hf-espidf-project-tools.git
```

### 2. Create Your First CI Workflow

```yaml
# .github/workflows/ci.yml
name: CI
on:
  push: { branches: [ main ] }
  pull_request: { branches: [ main ] }

jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/ru-build.yml@v1
    with:
      project_dir: examples/esp32
      project_tools_dir: hf-espidf-project-tools
      auto_clone_tools: true
      clean_build: false

  security:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      project_tools_dir: hf-espidf-project-tools
      scan_type: "all"
      run_codeql: true
```

## 📚 Workflow Details

### Build Workflow
- **Purpose**: Matrix builds across ESP-IDF versions and build types
- **Key Features**: Caching, artifact uploads, matrix generation
- **Use Case**: CI/CD for ESP32 applications

[→ Full Build Guide](build-workflow.md)


### Security Workflow
- **Purpose**: Comprehensive security auditing
- **Key Features**: Dependencies, secrets, CodeQL analysis
- **Use Case**: Security compliance and vulnerability detection

[→ Full Security Guide](security-workflow.md)

### Release Workflow
- **Purpose**: Automated firmware releases with build artifacts
- **Key Features**: GitHub releases, firmware binaries, auto-generated notes
- **Use Case**: Version releases with ESP32 firmware artifacts

[→ Full Release Guide](release-workflow.md)

## 🔄 Workflow Combinations

### Full CI Pipeline
```yaml
# Combines all workflows for comprehensive CI
jobs:
  build: # Matrix builds
  security: # Security audit
  release: # Firmware release (on tags)
```

### Security Pipeline
```yaml
# Security-focused workflows
jobs:
  security: # Full security audit
```

## 📖 Next Steps

1. **Choose your workflows** based on your project needs
2. **Read the individual guides** for detailed configuration
3. **Customize inputs** to match your project structure
4. **Test with a simple workflow** before adding complexity

## 🔗 Related Resources

- [Main Repository](https://github.com/N3b3x/hf-espidf-ci-tools)
- [Project Tools Repository](https://github.com/N3b3x/hf-espidf-project-tools)
- [ESP-IDF Documentation](https://docs.espressif.com/projects/esp-idf/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

---

[← Previous:🏠**Main README**](../README.md) | [Next: Build Workflow →](build-workflow.md)

