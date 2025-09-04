# 🔗 Documentation Link Check Workflow Guide

<div align="center">

![Link Check](https://img.shields.io/badge/Link%20Check-Documentation-blue?style=for-the-badge&logo=markdown)
![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-orange?style=for-the-badge&logo=github)
![Validation](https://img.shields.io/badge/Validation-Broken%20Links-red?style=for-the-badge&logo=link)

**🔍 Automated Documentation Link Validation for HardFOC Projects**

*Standalone workflow for checking documentation links and ensuring all references are valid*

</div>

---

## 📋 Overview

The **Link Check Workflow** is a standalone reusable workflow that validates all documentation links in your repository. It ensures that all internal and external links are working correctly, preventing broken references in your documentation.

### **Key Features**

- 🔍 **Comprehensive Link Checking** - Validates all markdown files and documentation
- 🌐 **External Link Validation** - Checks external URLs for accessibility
- 📁 **Internal Link Validation** - Verifies internal file references and anchors
- ⚡ **Fast Processing** - Efficient scanning with configurable timeouts
- 🎯 **Flexible Path Patterns** - Customizable file and directory patterns
- 📊 **Detailed Reporting** - Clear error messages and link status

---

## 🚀 Quick Start

### **Basic Usage**

```yaml
name: Check Documentation Links

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
```

### **Advanced Configuration**

```yaml
name: Advanced Link Check

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "docs/**,*.md,**/docs/**"
      fail_on_errors: true
```

---

## 📖 Input Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `paths` | `string` | `"docs/**,*.md,**/docs/**"` | Comma-separated paths to check for broken links |
| `fail_on_errors` | `boolean` | `true` | Fail the workflow if broken links are found |

### **Path Patterns**

The `paths` parameter supports glob patterns:

- `docs/**` - All files in docs directory and subdirectories
- `*.md` - All markdown files in repository root
- `**/docs/**` - All files in any docs directory
- `README.md` - Specific file
- `docs/**,*.md` - Multiple patterns (comma-separated)

---

## 🔧 Usage Examples

### **Basic Link Check**

```yaml
name: Basic Link Check

on:
  push:
    branches: [main]

jobs:
  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
```

### **Custom Paths**

```yaml
name: Custom Link Check

on:
  push:
    branches: [main]

jobs:
  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "docs/**,README.md,CONTRIBUTING.md"
      fail_on_errors: false
```

### **Documentation Only**

```yaml
name: Documentation Link Check

on:
  push:
    branches: [main]

jobs:
  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "docs/**"
```

### **All Markdown Files**

```yaml
name: All Markdown Link Check

on:
  push:
    branches: [main]

jobs:
  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "**/*.md"
```

---

## 🛠️ Integration with Other Workflows

### **Combined with Documentation Workflow**

```yaml
name: Documentation Pipeline

on:
  push:
    branches: [main]

jobs:
  docs:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/docs.yml@v1
    with:
      project_dir: docs
      run_link_check: true

  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "docs/**"
```

### **Part of Full CI Pipeline**

```yaml
name: Full CI Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32

  lint:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/lint.yml@v1

  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1

  security:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/security.yml@v1
    with:
      project_dir: examples/esp32
```

---

## 🔍 How It Works

### **Link Checking Process**

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ Scan Files      │───▶│ Extract Links   │───▶│ Validate Links  │───▶│ Report Results  │
│ (md-dead-link-  │    │ (Markdown       │    │ (HTTP requests  │    │ (Success/Error  │
│ check)          │    │ parsing)        │    │ + file checks)  │    │ messages)       │
└─────────────────┘    └─────────────────┘    └─────────────────┘    └─────────────────┘
```

### **Link Types Checked**

1. **External Links** - HTTP/HTTPS URLs
2. **Internal File Links** - Relative file paths
3. **Anchor Links** - Links to sections within files
4. **Image Links** - Image file references

### **Validation Methods**

- **External Links**: HTTP HEAD requests with timeout
- **Internal Links**: File system existence checks
- **Anchor Links**: Section header validation
- **Image Links**: File existence and format validation

---

## ⚙️ Configuration Options

### **md-dead-link-check Parameters**

The workflow uses `AlexanderDokuchaev/md-dead-link-check@v1.2.0` with these default settings:

- **Timeout**: 5 seconds per link
- **Error Codes**: 404, 410, 500 (configurable)
- **External Links**: Enabled
- **Anchor Checking**: Enabled
- **SSL Validation**: Enabled

### **Custom Configuration**

For advanced configuration, you can use a `pyproject.toml` file:

```toml
[tool.md_dead_link_check]
timeout = 10
exclude_links = ["https://github.com/", "https://github.com/*"]
exclude_files = ["CHANGELOG.md"]
check_web_links = true
catch_response_codes = [404, 410, 500]
validate_ssl = true
throttle_groups = 100
throttle_delay = 20
throttle_max_delay = 100
```

---

## 🚨 Troubleshooting

### **Common Issues**

#### **Link Check Fails**
**Symptoms**: Workflow fails with "broken links found"
**Solutions**:
```bash
# Check specific links manually
curl -I https://example.com/broken-link

# Verify internal file paths
ls -la docs/broken-file.md

# Check anchor references
grep -n "## Target Section" docs/file.md
```

#### **Timeout Errors**
**Symptoms**: "Timeout" or "Connection timeout" errors
**Solutions**:
```bash
# Check if external site is accessible
ping example.com

# Verify network connectivity
curl -I https://example.com

# Check if site requires authentication
```

#### **False Positives**
**Symptoms**: Valid links reported as broken
**Solutions**:
```bash
# Check link format
echo "https://example.com/path"

# Verify file encoding
file -i docs/file.md

# Check for special characters
grep -n "\[.*\](.*)" docs/file.md
```

### **Debug Mode**

Enable verbose output for debugging:

```yaml
jobs:
  link-check:
    uses: N3b3x/hf-espidf-ci-tools/.github/workflows/link-check.yml@v1
    with:
      paths: "docs/**"
      fail_on_errors: false  # Don't fail on errors for debugging
```

---

## 📊 Output and Reporting

### **Success Output**

```
✅ Link check completed successfully
📊 Checked 25 files
🔗 Validated 150 links
⏱️  Processing time: 45 seconds
```

### **Error Output**

```
❌ Link check failed
📊 Checked 25 files
🔗 Found 3 broken links
⏱️  Processing time: 45 seconds

Broken links:
- docs/guide.md:5 → https://example.com/broken
- docs/api.md:12 → ../missing-file.md
- README.md:8 → #non-existent-section
```

---

## 🔗 Related Documentation

- [Documentation Workflow](docs-workflow.md) - Build and deploy documentation
- [Build Workflow](build-workflow.md) - ESP-IDF matrix builds
- [Example Workflows](example-workflows.md) - Complete workflow examples
- [Main Documentation Index](index.md) - All workflow guides

---

## 📚 External Resources

- [md-dead-link-check Documentation](https://github.com/AlexanderDokuchaev/md-dead-link-check)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Markdown Link Syntax](https://www.markdownguide.org/basic-syntax/#links)

---

**← Back to [Documentation Index](index.md) | [Example Workflows](example-workflows.md) →**
