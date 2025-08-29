### ESP-IDF Build (Reusable)

Use this reusable workflow to build ESP-IDF projects from another repository.

Inputs:
- project_dir: path to project containing scripts/ and CMake files
- build_path: staging directory (default: ci_build_path)
- clean_build: boolean to skip caches

Matrix is produced by your project's scripts/generate_matrix.py.

Example usage:

```yaml
jobs:
  build:
    uses: your-org/nt-espidf-autobuild-tools/.github/workflows/build.yml@v1
    with:
      project_dir: examples/esp32
      build_path: ci_build_path
      clean_build: false
```
