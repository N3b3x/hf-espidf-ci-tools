### C/C++ Lint (Reusable)

Runs clang-format and clang-tidy using cpp-linter.

Inputs:
- paths: comma-separated globs (default: src/**,inc/**,examples/**)
- clang_version: major version (default: 20)

Example usage:

```yaml
jobs:
  lint:
    uses: your-org/nt-espidf-autobuild-tools/.github/workflows/lint.yml@v1
    with:
      paths: "src/**,inc/**,examples/**"
      clang_version: "20"
```
