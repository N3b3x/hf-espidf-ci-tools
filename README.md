# nt-espidf-ci-tools
Tools to help add CI/CD to ESP-IDF projects that include one or many applications based on a YAML configuration.

## 📚 Documentation

| Workflow | Purpose | Inputs | Outputs | Documentation |
|----------|---------|---------|---------|---------------|
| **Build** | ESP-IDF matrix builds with caching | `project_dir`, `scripts_dir`, `build_path` | Build artifacts, matrix | [Build Guide](docs/build-workflow.md) |
| **Docs** | Doxygen + GitHub Pages | `project_dir`, `scripts_dir`, `doxygen_config` | HTML docs, Pages deploy | [Docs Guide](docs/docs-workflow.md) |
| **Lint** | C/C++ code quality (clang-format/tidy) | `paths`, `clang_version` | PR annotations, style fixes | [Lint Guide](docs/lint-workflow.md) |
| **Static Analysis** | Cppcheck security analysis | `paths`, `std`, `strict` | XML reports, artifacts | [Static Analysis Guide](docs/static-analysis-workflow.md) |
| **Security** | Dependencies, secrets, CodeQL | `project_dir`, `scripts_dir`, `scan_type` | Security reports, SBOM | [Security Guide](docs/security-workflow.md) |

📖 **Start here**: [Documentation Index](docs/index.md) - Complete guide with examples and navigation

### What this repo provides
- Reusable GitHub Actions workflows for ESP-IDF build, docs, lint, static analysis, and security
- Composite actions to stage a CI build workspace and run C/C++ linters
- Comprehensive documentation with examples and navigation

### Quick Start

**1. Clone the tools repo in your project:**
```bash
git clone https://github.com/N3b3x/nt-espidf-tools.git
```

**2. Use the workflows in your CI:**
```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]
jobs:
  build:
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
      auto_clone_tools: true
      tools_repo_url: https://github.com/N3b3x/nt-espidf-tools.git
```

**3. See [Documentation Index](docs/index.md) for complete examples and guides**

### Consumer usage (examples)

CI combining build + lint + static analysis:
```yaml
name: CI
on:
  push: { branches: [ main ] }
  pull_request: { branches: [ main ] }

jobs:
  build:
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32   # Change to where your esp32 project directory is
      scripts_dir: nt-espidf-tools  
      auto_clone_tools: true
      tools_repo_url: https://github.com/N3b3x/nt-espidf-tools.git
      tools_repo_ref: main
      build_path: ci_build_path
      clean_build: false

  lint:
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"

  static:
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/static-analysis.yml@v1
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
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/docs.yml@v1
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
    uses: N3b3x/nt-espidf-project-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      scripts_dir: nt-espidf-tools
      scan_type: "all"
      run_codeql: true
      codeql_languages: "cpp"
```

---

📖 **Next**: [Documentation Index](docs/index.md) - Complete navigation and examples
