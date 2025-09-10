---
layout: default
title: "🔧 HardFOC ESP32 CI Tools"
description: "Advanced CI/CD Tools for HardFOC ESP32 Projects - Comprehensive GitHub Actions workflows for ESP-IDF development with matrix builds, security auditing, and automated documentation"
nav_order: 1
permalink: /
---

# 🔧 HardFOC ESP32 CI Tools

![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-blue?style=for-the-badge&logo=github-actions)
![ESP-IDF](https://img.shields.io/badge/ESP--IDF-Matrix%20Builds-green?style=for-the-badge&logo=espressif)
![HardFOC](https://img.shields.io/badge/HardFOC-ESP32-orange?style=for-the-badge&logo=espressif)

**🚀 Advanced CI/CD Tools for HardFOC ESP32 Projects**

*Comprehensive GitHub Actions workflows for ESP-IDF development with matrix builds, security auditing, and automated documentation*

---

## 📚 Documentation

**🌐 [Live Documentation Site](https://n3b3x.github.io/hf-espidf-project-tools/)**  
*Published documentation with enhanced navigation and search*

| Workflow | Purpose | Key Features | Documentation |
|----------|---------|--------------|---------------|
| **🏗️ Build** | Matrix ESP-IDF builds | Parallel builds, caching, size reports | [Build Guide](docs/build-workflow.md) |
| **🛡️ Security** | Security auditing | CodeQL, dependency scan, secrets | [Security Guide](docs/security-workflow.md) |

📖 **Start here**: [Documentation Index](docs/index.md) - Complete guide with examples and navigation  
🚀 **Examples**: [Example Workflows](docs/example-workflows.md) - Ready-to-use workflow templates

---

## 🎯 What This Repository Provides

### **Reusable GitHub Actions Workflows**
- ✅ **Matrix-based ESP-IDF builds** across multiple versions and configurations
- ✅ **Comprehensive security auditing** with CodeQL and dependency scanning

### **Smart Project Tools Integration**
- ✅ **Single composite action** for project tools directory management
- ✅ **Automatic tool cloning** with security validation
- ✅ **Dynamic matrix generation** from project configuration
- ✅ **Intelligent caching** for faster builds

### **HardFOC Project Support**
- ✅ **ESP32-C6 optimized** for HardFOC interface projects
- ✅ **Multi-application support** with YAML configuration
- ✅ **Parallel execution** for maximum CI efficiency
- ✅ **Comprehensive documentation** with examples and troubleshooting

---

## 🚀 Quick Start

### **1. Basic Usage**

```yaml
# .github/workflows/ci.yml
name: ESP32 CI

on: [push, pull_request]

jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
```

### **2. Advanced Parallel Workflow**

```yaml
name: 🚀 Advanced ESP32 CI

on: [push, pull_request]

jobs:
  # Build firmware across multiple configurations
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32

  # Security audit
  security:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      scan_type: "all"
      run_codeql: true
```

### **3. Complete Examples**

For comprehensive examples including development, release, and security-focused workflows, see [**Example Workflows**](docs/example-workflows.md).

### Consumer usage (examples)

CI combining build + security:
```yaml
name: CI
on:
  push: { branches: [ main ] }
  pull_request: { branches: [ main ] }

jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32   # Change to where your esp32 project directory is
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
      codeql_languages: "cpp"
```

---

📖 **Next**: [Documentation Index](docs/index.md) - Complete navigation and examples
