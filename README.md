# 🔧 HardFOC ESP32 CI Tools

<div align="center">

![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-blue?style=for-the-badge&logo=github-actions)
![ESP-IDF](https://img.shields.io/badge/ESP--IDF-Matrix%20Builds-green?style=for-the-badge&logo=espressif)
![HardFOC](https://img.shields.io/badge/HardFOC-ESP32-orange?style=for-the-badge&logo=espressif)

**🚀 Advanced CI/CD Tools for HardFOC ESP32 Projects**

*Comprehensive GitHub Actions workflows for ESP-IDF development with matrix builds, security auditing, and automated documentation*

</div>

---

## 📚 Documentation

| Workflow | Purpose | Key Features | Documentation |
|----------|---------|--------------|---------------|
| **🏗️ Build** | Matrix ESP-IDF builds | Parallel builds, caching, size reports | [Build Guide](docs/build-workflow.md) |
| **🔧 Lint** | C/C++ code quality | Auto-fix, clang-format, clang-tidy | [Lint Guide](docs/lint-workflow.md) |
| **📚 Docs** | Documentation & Pages | Doxygen, GitHub Pages, link checking | [Docs Guide](docs/docs-workflow.md) |
| **🛡️ Security** | Security auditing | CodeQL, dependency scan, secrets | [Security Guide](docs/security-workflow.md) |
| **🔍 Static Analysis** | Code quality analysis | Cppcheck, security patterns | [Static Analysis Guide](docs/static-analysis-workflow.md) |
| **🔗 Link Check** | Documentation links | Dead link detection, validation | [Link Check Guide](docs/link-check-workflow.md) |

📖 **Start here**: [Documentation Index](docs/index.md) - Complete guide with examples and navigation  
🚀 **Examples**: [Example Workflows](docs/example-workflows.md) - Ready-to-use workflow templates

---

## 🎯 What This Repository Provides

### **Reusable GitHub Actions Workflows**
- ✅ **Matrix-based ESP-IDF builds** across multiple versions and configurations
- ✅ **Automated code linting and formatting** with clang-format and clang-tidy
- ✅ **Comprehensive security auditing** with CodeQL and dependency scanning
- ✅ **Documentation building and deployment** with Doxygen and GitHub Pages
- ✅ **Static code analysis** with Cppcheck for security and quality
- ✅ **Link validation** for documentation integrity

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
      tools_repo_sha: ${{ github.sha }}

  # Lint and auto-fix code
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      auto_fix: true
      commit: true

  # Security audit
  security:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      scan_type: "all"
      run_codeql: true

  # Documentation
  docs:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/docs.yml@v1
    with:
      project_dir: examples/esp32
      deploy_pages: true
```

### **3. Complete Examples**

For comprehensive examples including development, release, and security-focused workflows, see [**Example Workflows**](docs/example-workflows.md).

### Consumer usage (examples)

CI combining build + lint + static analysis:
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
      scripts_dir: nt-espidf-tools  
      auto_clone_tools: true
      tools_repo_url: https://github.com/N3b3x/nt-espidf-tools.git
      tools_repo_ref: main
      build_path: ci_build_path
      clean_build: false

  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      clang_version: "20"
      # auto_fix: true  # Uncomment to auto-format and push changes

  static:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/static-analysis.yml@v1
    with:
      paths: "src inc examples"
      strict: false
```

Docs build & deploy:
```yaml
name: Docs
on:
  push: { branches: [ main ] }

permissions:
  contents: write
  pages: write
  id-token: write

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

Link checking:
```yaml
name: Link Check
on:
  pull_request: { branches: [ main ] }
  push: { branches: [ main ] }

jobs:
  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "docs/**,*.md,**/docs/**"
      fail_on_errors: true
      # Uses external md-dead-link-check action directly
```

Security audits:
```yaml
name: Security
on:
  pull_request: { branches: [ main ] }
  schedule:
    - cron: '0 8 * * 1'

permissions:
  contents: read
  security-events: write

jobs:
  security:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
      scan_type: "all"
      run_codeql: true
      codeql_languages: "cpp"
```

---

📖 **Next**: [Documentation Index](docs/index.md) - Complete navigation and examples
