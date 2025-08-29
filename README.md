# nt-espidf-ci-tools
Tools to help add CI/CD to ESP-IDF projects that include one or many applications based on a YAML configuration.

### What this repo provides
- Reusable GitHub Actions workflows for ESP-IDF build, docs, lint, static analysis, and security
- Composite actions to stage a CI build workspace and run C/C++ linters
- Minimal docs with examples on how to call from consumer repos

### Consumer usage (examples)

CI combining build + lint + static analysis:
```yaml
name: CI
on:
  push: { branches: [ main ] }
  pull_request: { branches: [ main ] }

jobs:
  build:
    uses: your-org/nt-espidf-autobuild-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      build_path: ci_build_path
      clean_build: false

  lint:
    uses: your-org/nt-espidf-autobuild-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"

  static:
    uses: your-org/nt-espidf-autobuild-tools/.github/workflows/static-analysis.yml@v1
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
    uses: your-org/nt-espidf-autobuild-tools/.github/workflows/docs.yml@v1
    with:
      project_dir: .
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
    uses: your-org/nt-espidf-autobuild-tools/.github/workflows/security.yml@v1
    with:
      scan_type: "all"
      run_codeql: true
      codeql_languages: "cpp"
```

See docs/ for more details.
