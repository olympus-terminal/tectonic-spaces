# Package Compatibility

Tectonic uses a curated bundle of TeX Live packages. Not everything is available.

## Known Working Packages

| Package | Notes |
|---------|-------|
| `graphicx` | Standard figure inclusion |
| `amsmath` | Math environments |
| `amssymb` | Math symbols |
| `booktabs` | Professional tables |
| `hyperref` | Links and PDF metadata |
| `natbib` | Bibliography (author-year) |
| `biblatex` | Modern bibliography |
| `xcolor` | Colors |
| `geometry` | Page layout |
| `fancyhdr` | Headers/footers |
| `listings` | Code listings |
| `algorithm2e` | Algorithms |
| `tikz` | Graphics |
| `pgfplots` | Plots |
| `fontspec` | Custom fonts (XeLaTeX mode) |
| `siunitx` | SI units |
| `cleveref` | Smart references |
| `subcaption` | Subfigures |
| `multirow` | Table spanning |
| `longtable` | Multi-page tables |

## Known Issues / Unavailable

| Package | Status | Workaround |
|---------|--------|------------|
| `minted` | Requires Python/Pygments | Use `listings` instead |
| Some fonts | Not all TL fonts included | Check bundle contents |

## Checking Package Availability

If a package fails to load:
1. Check the error message for the specific file
2. Search tectonic bundle contents
3. Look for alternatives

## Adding to This List

Document your findings:
```markdown
| `packagename` | Works/Fails | Notes about usage |
```
