---
layout: default
title: "🚀 Example Workflows for Consumer Repositories"
description: "Complete workflow examples for HardFOC ESP32 projects - Ready-to-use GitHub Actions workflows that leverage all CI tools in parallel"
nav_order: 5
parent: "📚 Documentation"
permalink: /docs/example-workflows/
---

# 🚀 Example Workflows for Consumer Repositories

**📋 Complete Workflow Examples for HardFOC ESP32 Projects**

*Ready-to-use GitHub Actions workflows that leverage all CI tools in parallel*

---

## 📋 Table of Contents

- [🎯 Overview](#-overview)
- [🏗️ Basic Workflow](#-basic-workflow)
- [🚀 Advanced Parallel Workflow](#-advanced-parallel-workflow)
- [🔧 Development Workflow](#-development-workflow)
- [📦 Release Workflow](#-release-workflow)
- [🛡️ Security-First Workflow](#-security-first-workflow)
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

  # Security audit
  security:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      scan_type: "all"
      run_codeql: true
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

  # 📊 Build summary and notifications
  summary:
    needs: [build, security]
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
          echo "| 🛡️ Security | ${{ needs.security.result == 'success' && '✅ Success' || '❌ Failed' }} |" >> $GITHUB_STEP_SUMMARY
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

  # Security audit
  security:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
      scan_type: "dependencies"  # Quick dependency scan for dev
      run_codeql: false
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

  # Full security audit
  security-release:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: ${{ env.PROJECT_DIR }}
      scan_type: "all"
      run_codeql: true
      codeql_languages: "cpp,python"

  # Create release artifacts
  create-release:
    needs: [build-release, security-release]
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

  # Build only if security passes
  build-secure:
    needs: security-audit
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      clean_build: true

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

  # Quick security scan
  security-quick:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: ${{ env.PROJECT_DIR }}
      scan_type: "dependencies"  # Only dependency scan for speed
      run_codeql: false

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
| **Simple Projects** | Basic Workflow | Build + Security |
| **Production Projects** | Advanced Parallel | All checks in parallel |
| **Development** | Development Workflow | Quick security scan |
| **Releases** | Release Workflow | Comprehensive + artifacts |
| **Security Critical** | Security-First | Security-first approach |
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
    with:
      project_dir: examples/esp32
      scan_type: "all"
      run_codeql: true
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
- [Security Workflow](security-workflow.md)

---

[← Previous: Security Workflow](security-workflow.md) | [Next: Documentation Index →](index.md)

**📚 [All Documentation](index.md)** | **🏠 [Main README](../README.md)**

