### Security Audit (Reusable)

Aggregates dependency scans (pip-audit, safety, bandit), secret scanning (gitleaks), and optional CodeQL.

Inputs:
- scan_type: all | dependencies | secrets | codeql (default: all)
- run_codeql: enable CodeQL job (default: true)
- codeql_languages: e.g., "cpp,python" (default: "cpp")
- codeql_build_cmd: optional build command for CodeQL
- project_dir: directory to run codeql_build_cmd in (default: ".")

Example usage:

```yaml
jobs:
  security:
    uses: your-org/nt-espidf-autobuild-tools/.github/workflows/security.yml@v1
    with:
      scan_type: "all"
      run_codeql: true
      codeql_languages: "cpp"
      # codeql_build_cmd: "idf.py build"
      # project_dir: "examples/esp32"
```
