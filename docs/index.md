# Documentation Index

Welcome to the hf-espidf-ci-tools documentation! This guide provides comprehensive coverage of all reusable workflows and how to integrate them into your ESP-IDF projects.

## 🚀 Quick Navigation

| Workflow | Description | Quick Start |
|----------|-------------|-------------|
| **[Build](build-workflow.md)** | ESP-IDF matrix builds with caching | [→ Build Guide](build-workflow.md) |
| **[Docs](docs-workflow.md)** | Doxygen + GitHub Pages deployment | [→ Docs Guide](docs-workflow.md) |
| **[Lint](lint-workflow.md)** | C/C++ code quality checks | [→ Lint Guide](lint-workflow.md) |
| **[Link Check](link-check-workflow.md)** | Documentation link validation | [→ Link Check Guide](link-check-workflow.md) |
| **[Static Analysis](static-analysis-workflow.md)** | Cppcheck security analysis | [→ Static Analysis Guide](static-analysis-workflow.md) |
| **[Security](security-workflow.md)** | Dependencies, secrets, CodeQL | [→ Security Guide](security-workflow.md) |

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
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      project_tools_dir: hf-espidf-project-tools
      auto_clone_tools: true
      tools_repo_url: https://github.com/N3b3x/hf-espidf-project-tools.git
      tools_repo_ref: main
      clean_build: false

  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"

  static:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/static-analysis.yml@v1
    with:
      paths: "src inc examples"
      strict: false
```

## 📚 Workflow Details

### Build Workflow
- **Purpose**: Matrix builds across ESP-IDF versions and build types
- **Key Features**: Caching, artifact uploads, matrix generation
- **Use Case**: CI/CD for ESP32 applications

[→ Full Build Guide](build-workflow.md)

### Documentation Workflow
- **Purpose**: Generate and deploy Doxygen documentation
- **Key Features**: GitHub Pages, link checking, artifact storage
- **Use Case**: Project documentation automation

[→ Full Docs Guide](docs-workflow.md)

### Linting Workflow
- **Purpose**: C/C++ code quality enforcement
- **Key Features**: clang-format, clang-tidy, PR annotations
- **Use Case**: Code style consistency

[→ Full Lint Guide](lint-workflow.md)

### Link Check Workflow
- **Purpose**: Documentation link validation
- **Key Features**: External/internal link checking, anchor validation
- **Use Case**: Documentation integrity

[→ Full Link Check Guide](link-check-workflow.md)

### Static Analysis Workflow
- **Purpose**: Security and quality analysis with cppcheck
- **Key Features**: Docker-based analysis, XML reports, configurable strictness
- **Use Case**: Security scanning and bug detection

[→ Full Static Analysis Guide](static-analysis-workflow.md)

### Security Workflow
- **Purpose**: Comprehensive security auditing
- **Key Features**: Dependencies, secrets, CodeQL analysis
- **Use Case**: Security compliance and vulnerability detection

[→ Full Security Guide](security-workflow.md)

## 🔄 Workflow Combinations

### Full CI Pipeline
```yaml
# Combines all workflows for comprehensive CI
jobs:
  build: # Matrix builds
  lint:  # Code quality
  static: # Security analysis
  security: # Security audit
```

### Documentation Pipeline
```yaml
# Documentation generation and deployment
jobs:
  docs: # Build and deploy docs
```

### Security Pipeline
```yaml
# Security-focused workflows
jobs:
  security: # Full security audit
  static: # Additional static analysis
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

**← Back to [Main README](../README.md)**
