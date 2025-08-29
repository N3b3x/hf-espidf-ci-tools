### Docs Build & Deploy (Reusable)

Build Doxygen documentation and optionally deploy to GitHub Pages.

Inputs:
- project_dir: path containing Doxyfile
- doxygen_config: path to Doxyfile (default: Doxyfile)
- output_dir: generated HTML directory (default: docs/doxygen/html)
- run_link_check: run docs/check_docs.py if present
- deploy_pages: deploy to Pages when on main and not PR

Example usage:

```yaml
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
