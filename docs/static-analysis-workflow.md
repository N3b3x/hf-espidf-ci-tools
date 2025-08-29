### Static Analysis (Cppcheck) (Reusable)

Runs cppcheck via Docker image and uploads XML + text output as artifacts.

Inputs:
- paths: space-separated directories (default: "src inc examples")
- std: C++ standard (default: c++17)
- strict: if true, fail job when issues are found

Example usage:

```yaml
jobs:
  static:
    uses: your-org/nt-espidf-autobuild-tools/.github/workflows/static-analysis.yml@v1
    with:
      paths: "src inc examples"
      std: "c++17"
      strict: false
```
