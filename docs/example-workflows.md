# 🚀 Example Workflows for Consumer Repositories

<div align="center">

![Examples](https://img.shields.io/badge/Examples-Consumer%20Workflows-blue?style=for-the-badge&logo=github)
![Parallel](https://img.shields.io/badge/Parallel-Jobs-green?style=for-the-badge&logo=github-actions)
![HardFOC](https://img.shields.io/badge/HardFOC-ESP32-orange?style=for-the-badge&logo=espressif)

**📋 Complete Workflow Examples for HardFOC ESP32 Projects**

*Ready-to-use GitHub Actions workflows that leverage all CI tools in parallel*

</div>

---

## 📋 Table of Contents

- [🎯 Overview](#-overview)
- [🏗️ Basic Workflow](#️-basic-workflow)
- [🚀 Advanced Parallel Workflow](#-advanced-parallel-workflow)
- [🔧 Development Workflow](#-development-workflow)
- [📦 Release Workflow](#-release-workflow)
- [🛡️ Security-First Workflow](#️-security-first-workflow)
- [📚 Documentation Workflow](#-documentation-workflow)
- [⚡ Performance Optimized](#-performance-optimized)

---

## 🎯 Overview

These example workflows demonstrate how to use the HardFOC ESP32 CI tools in your consumer repositories. Each workflow is designed for different use cases and can be customized to fit your project's needs.

### **Key Features**
- 🔄 **Parallel Execution** - Multiple jobs run simultaneously for maximum efficiency
- 🛡️ **Comprehensive Coverage** - Build, lint, test, security, and documentation
- 📊 **Smart Caching** - Optimized for fast builds and minimal resource usage
- 🎯 **HardFOC Optimized** - Specifically designed for ESP32 development workflows

---

## 🏗️ Basic Workflow

**Use Case**: Simple projects that need basic CI/CD with build and lint checks.

```yaml
name: 🏗️ Basic ESP32 CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  # Build firmware across multiple configurations
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32

  # Lint C/C++ code
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      auto_fix: false

  # Check documentation links
  docs-links:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "docs/**,*.md,**/docs/**"
```

---

## 🚀 Advanced Parallel Workflow

**Use Case**: Production projects requiring comprehensive CI/CD with all checks running in parallel.

```yaml
name: 🚀 Advanced ESP32 CI

on:
  push:
    branches: [main, develop, release/*]
  pull_request:
    branches: [main, develop]
  workflow_dispatch:
    inputs:
      clean_build:
        description: 'Clean build (no cache)'
        required: false
        default: false
        type: boolean
      run_security:
        description: 'Run security checks'
        required: false
        default: true
        type: boolean

env:
  PROJECT_DIR: examples/esp32
  TOOLS_DIR: hf-espidf-project-tools

jobs:
  # 🔍 Matrix generation and validation
  validate:
    runs-on: ubuntu-latest
    outputs:
      matrix: ${{ steps.matrix.outputs.matrix }}
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive
      
      - name: Validate configuration
        run: |
          cd ${{ env.PROJECT_DIR }}
          python3 ${{ env.TOOLS_DIR }}/generate_matrix.py --validate --verbose

      - name: Generate build matrix
        id: matrix
        run: |
          cd ${{ env.PROJECT_DIR }}
          MATRIX=$(python3 ${{ env.TOOLS_DIR }}/generate_matrix.py)
          echo "matrix=${MATRIX}" >> "$GITHUB_OUTPUT"
          echo "Generated matrix with $(echo "$MATRIX" | jq '.include | length') build combinations"

  # 🏗️ Build firmware (parallel matrix)
  build:
    needs: validate
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: ${{ env.PROJECT_DIR }}
      project_tools_dir: ${{ env.TOOLS_DIR }}
      clean_build: ${{ github.event.inputs.clean_build == 'true' }}
      tools_repo_sha: ${{ github.sha }}

  # 🔧 Lint and format code
  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**,components/**"
      auto_fix: true
      commit: true
      commit_message: "🔧 Auto-format code with clang-format"
      git_name: "HardFOC Bot"
      git_email: "bot@hardfoc.com"
      style: "file"
      tidy_checks: "readability-*,performance-*,modernize-*"
      files_changed_only: true
      step_summary: true
      file_annotations: true

  # 🛡️ Security audit
  security:
    if: ${{ github.event.inputs.run_security != 'false' }}
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: ${{ env.PROJECT_DIR }}
      project_tools_dir: ${{ env.TOOLS_DIR }}
      scan_type: "all"
      run_codeql: true
      codeql_languages: "cpp,python"
      tools_repo_sha: ${{ github.sha }}

  # 🔍 Static analysis
  static-analysis:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/static-analysis.yml@v1
    with:
      paths: "src inc examples components"
      std: "c++17"
      strict: false

  # 📚 Build and check documentation
  docs:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/docs.yml@v1
    with:
      project_dir: ${{ env.PROJECT_DIR }}
      doxygen_config: "Doxyfile"
      output_dir: "docs/doxygen/html"
      run_link_check: true
      link_check_paths: "docs/**,*.md,**/docs/**"
      deploy_pages: ${{ github.ref == 'refs/heads/main' }}

  # 🔗 Check documentation links
  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "docs/**,*.md,**/docs/**,README.md"
      fail_on_errors: true

  # 📊 Build summary and notifications
  summary:
    needs: [build, lint, security, static-analysis, docs, link-check]
    if: always()
    runs-on: ubuntu-latest
    steps:
      - name: Build Summary
        run: |
          echo "## 🚀 Build Summary" >> $GITHUB_STEP_SUMMARY
          echo "" >> $GITHUB_STEP_SUMMARY
          echo "| Job | Status |" >> $GITHUB_STEP_SUMMARY
          echo "|-----|--------|" >> $GITHUB_STEP_SUMMARY
          echo "| 🏗️ Build | ${{ needs.build.result == 'success' && '✅ Success' || '❌ Failed' }} |" >> $GITHUB_STEP_SUMMARY
          echo "| 🔧 Lint | ${{ needs.lint.result == 'success' && '✅ Success' || '❌ Failed' }} |" >> $GITHUB_STEP_SUMMARY
          echo "| 🛡️ Security | ${{ needs.security.result == 'success' && '✅ Success' || '❌ Failed' }} |" >> $GITHUB_STEP_SUMMARY
          echo "| 🔍 Static Analysis | ${{ needs.static-analysis.result == 'success' && '✅ Success' || '❌ Failed' }} |" >> $GITHUB_STEP_SUMMARY
          echo "| 📚 Docs | ${{ needs.docs.result == 'success' && '✅ Success' || '❌ Failed' }} |" >> $GITHUB_STEP_SUMMARY
          echo "| 🔗 Link Check | ${{ needs.link-check.result == 'success' && '✅ Success' || '❌ Failed' }} |" >> $GITHUB_STEP_SUMMARY
```

---

## 🔧 Development Workflow

**Use Case**: Development branches with auto-fix and relaxed checks.

```yaml
name: 🔧 Development CI

on:
  push:
    branches: [develop, feature/*, bugfix/*]
  pull_request:
    branches: [develop]

jobs:
  # Quick build for development
  build-dev:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      clean_build: false  # Use caches for faster builds

  # Auto-fix code formatting
  lint-fix:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      auto_fix: true
      commit: true
      commit_message: "🔧 Auto-format code [skip ci]"
      files_changed_only: true
      style: "file"
      tidy_checks: "readability-*"  # Only readability checks for dev

  # Quick link check
  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "*.md,README.md"
      fail_on_errors: false  # Don't fail on broken links in dev
```

---

## 📦 Release Workflow

**Use Case**: Release branches with comprehensive checks and artifact publishing.

```yaml
name: 📦 Release CI

on:
  push:
    branches: [release/*, main]
    tags: ['v*']
  workflow_dispatch:
    inputs:
      release_version:
        description: 'Release version (e.g., v1.0.0)'
        required: true
        type: string

env:
  PROJECT_DIR: examples/esp32
  RELEASE_VERSION: ${{ github.event.inputs.release_version || github.ref_name }}

jobs:
  # Comprehensive build for release
  build-release:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: ${{ env.PROJECT_DIR }}
      clean_build: true  # Clean build for release
      tools_repo_sha: ${{ github.sha }}

  # Strict linting for release
  lint-release:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**,components/**"
      auto_fix: false  # No auto-fix for release
      style: "file"
      tidy_checks: "readability-*,performance-*,modernize-*,cert-*"
      strict: true

  # Full security audit
  security-release:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: ${{ env.PROJECT_DIR }}
      scan_type: "all"
      run_codeql: true
      codeql_languages: "cpp,python"
      tools_repo_sha: ${{ github.sha }}

  # Comprehensive static analysis
  static-analysis-release:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/static-analysis.yml@v1
    with:
      paths: "src inc examples components"
      std: "c++17"
      strict: true  # Fail on any issues

  # Build and deploy documentation
  docs-release:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/docs.yml@v1
    with:
      project_dir: ${{ env.PROJECT_DIR }}
      doxygen_config: "Doxyfile"
      output_dir: "docs/doxygen/html"
      run_link_check: true
      deploy_pages: true

  # Create release artifacts
  create-release:
    needs: [build-release, lint-release, security-release, static-analysis-release, docs-release]
    if: startsWith(github.ref, 'refs/tags/')
    runs-on: ubuntu-latest
    steps:
      - name: Download build artifacts
        uses: actions/download-artifact@v4
        with:
          pattern: fw-*
          merge-multiple: true
          path: artifacts/

      - name: Create Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ env.RELEASE_VERSION }}
          release_name: Release ${{ env.RELEASE_VERSION }}
          body: |
            ## 🚀 HardFOC ESP32 Release ${{ env.RELEASE_VERSION }}
            
            ### 📦 Firmware Binaries
            - All firmware binaries are attached to this release
            - Built with ESP-IDF v5.5
            - Target: ESP32-C6
            
            ### 📚 Documentation
            - [Documentation](https://${{ github.repository_owner }}.github.io/${{ github.event.repository.name }}/)
            
            ### 🛡️ Security
            - Full security audit completed
            - CodeQL analysis passed
            - No known vulnerabilities
          draft: false
          prerelease: false

      - name: Upload Release Assets
        uses: actions/upload-release-asset@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          upload_url: ${{ steps.create_release.outputs.upload_url }}
          asset_path: artifacts/
          asset_name: firmware-${{ env.RELEASE_VERSION }}.zip
          asset_content_type: application/zip
```

---

## 🛡️ Security-First Workflow

**Use Case**: Security-critical projects requiring comprehensive security checks.

```yaml
name: 🛡️ Security-First CI

on:
  push:
    branches: [main, security/*]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 2 * * 1'  # Weekly security scan

jobs:
  # Security audit (runs first)
  security-audit:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      scan_type: "all"
      run_codeql: true
      codeql_languages: "cpp,python"
      tools_repo_sha: ${{ github.sha }}

  # Build only if security passes
  build-secure:
    needs: security-audit
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      clean_build: true
      tools_repo_sha: ${{ github.sha }}

  # Strict linting with security focus
  lint-secure:
    needs: security-audit
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**,components/**"
      auto_fix: false
      style: "file"
      tidy_checks: "cert-*,cppcoreguidelines-*,readability-*"
      strict: true

  # Additional security checks
  security-scan:
    needs: security-audit
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'

      - name: Upload Trivy scan results
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'
```

---

## 📚 Documentation Workflow

**Use Case**: Documentation-focused projects with comprehensive doc checks.

```yaml
name: 📚 Documentation CI

on:
  push:
    branches: [main, docs/*]
  pull_request:
    branches: [main]
    paths: ['docs/**', '*.md', 'README.md']

jobs:
  # Build documentation
  docs-build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/docs.yml@v1
    with:
      project_dir: examples/esp32
      doxygen_config: "Doxyfile"
      output_dir: "docs/doxygen/html"
      run_link_check: true
      link_check_paths: "docs/**,*.md,**/docs/**,README.md"
      deploy_pages: ${{ github.ref == 'refs/heads/main' }}

  # Comprehensive link checking
  link-check-comprehensive:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "docs/**,*.md,**/docs/**,README.md,**/*.md"
      fail_on_errors: true

  # Documentation linting
  docs-lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install markdownlint
        run: npm install -g markdownlint-cli

      - name: Lint markdown files
        run: markdownlint "**/*.md" --ignore node_modules

  # Spell checking
  spell-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup cspell
        run: npm install -g cspell

      - name: Spell check
        run: cspell "**/*.md" --config .cspell.json
```

---

## ⚡ Performance Optimized

**Use Case**: Large projects requiring maximum build performance and minimal resource usage.

```yaml
name: ⚡ Performance CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  PROJECT_DIR: examples/esp32
  CACHE_VERSION: v1

jobs:
  # Optimized build with aggressive caching
  build-fast:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: ${{ env.PROJECT_DIR }}
      clean_build: false  # Always use caches
      build_path: "fast_build_${{ github.run_id }}"

  # Parallel linting with minimal checks
  lint-fast:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**"
      auto_fix: true
      files_changed_only: true  # Only check changed files
      lines_changed_only: true  # Only check changed lines
      style: "file"
      tidy_checks: "readability-*"  # Minimal checks for speed

  # Quick security scan
  security-quick:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: ${{ env.PROJECT_DIR }}
      scan_type: "dependencies"  # Only dependency scan for speed
      run_codeql: false

  # Fast link check
  link-check-fast:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "README.md,*.md"  # Only check main docs
      fail_on_errors: false  # Don't fail on broken links for speed

  # Performance monitoring
  performance-check:
    runs-on: ubuntu-latest
    steps:
      - name: Check build times
        run: |
          echo "## ⚡ Performance Metrics" >> $GITHUB_STEP_SUMMARY
          echo "Build completed in: ${{ github.run_duration }}" >> $GITHUB_STEP_SUMMARY
          echo "Cache hit rate: High (optimized configuration)" >> $GITHUB_STEP_SUMMARY
```

---

## 🎯 Workflow Selection Guide

| Use Case | Recommended Workflow | Key Features |
|----------|---------------------|--------------|
| **Simple Projects** | Basic Workflow | Build + Lint + Link Check |
| **Production Projects** | Advanced Parallel | All checks in parallel |
| **Development** | Development Workflow | Auto-fix, relaxed checks |
| **Releases** | Release Workflow | Comprehensive + artifacts |
| **Security Critical** | Security-First | Security-first approach |
| **Documentation Heavy** | Documentation Workflow | Doc-focused checks |
| **Large Projects** | Performance Optimized | Maximum speed |

---

## 🔧 Customization Tips

### **Environment Variables**
```yaml
env:
  PROJECT_DIR: examples/esp32
  TOOLS_DIR: hf-espidf-project-tools
  CACHE_VERSION: v1
  BUILD_TIMEOUT: 30
```

### **Conditional Execution**
```yaml
jobs:
  security:
    if: github.event_name == 'pull_request' || github.ref == 'refs/heads/main'
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
```

### **Matrix Strategies**
```yaml
strategy:
  fail-fast: false  # Don't stop on first failure
  max-parallel: 4   # Limit parallel jobs
```

### **Cache Optimization**
```yaml
- name: Cache ESP-IDF
  uses: actions/cache@v4
  with:
    path: ~/.espressif
    key: esp-idf-${{ runner.os }}-${{ hashFiles('**/app_config.yml') }}
```

---

## 📚 Next Steps

1. **Choose your workflow** based on your project needs
2. **Customize the configuration** for your specific setup
3. **Add your project structure** following the requirements
4. **Test locally** using the provided scripts
5. **Monitor and optimize** based on your build times and requirements

For more detailed information, see the individual workflow documentation:
- [Build Workflow](build-workflow.md)
- [Lint Workflow](lint-workflow.md)
- [Security Workflow](security-workflow.md)
- [Documentation Workflow](docs-workflow.md)
