# Tectonic Usage Solutions

Documentation of issues, solutions, and best practices for using [Tectonic](https://tectonic-typesetting.github.io/) - the modernized, self-contained LaTeX engine.

## Environment

- **Tectonic Version:** 0.15.0
- **Platform:** macOS (Apple Silicon)
- **Install method:** Homebrew (`brew install tectonic`)

## Quick Reference

```bash
# Basic compilation
tectonic document.tex

# Keep intermediate files
tectonic --keep-intermediates document.tex

# Verbose output for debugging
tectonic -X compile document.tex

# Use specific bundle
tectonic --bundle https://data1.fullyjustified.net/tlextras-2023.1r0.tar document.tex
```

## Contents

- [Common Issues & Solutions](docs/common-issues.md)
- [Best Practices](docs/best-practices.md)
- [Package Compatibility](docs/package-compatibility.md)
- [Workflow Tips](docs/workflow-tips.md)
- [TikZ Scientific Diagrams](docs/tikz-diagrams.md) - Publication-quality flowcharts

## Why Tectonic?

- Self-contained: downloads packages automatically
- Reproducible builds
- Modern Rust implementation
- No TeX Live installation needed
- Runs until stable (no manual re-runs for references)
