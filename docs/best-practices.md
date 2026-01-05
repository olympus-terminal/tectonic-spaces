# Best Practices

## Project Structure

```
project/
├── manuscript.tex          # Main document
├── references.bib          # Bibliography
├── figures/               # Figure files
│   ├── fig1.pdf
│   └── fig2.pdf
├── sections/              # Optional: split large docs
│   ├── introduction.tex
│   └── methods.tex
└── .gitignore
```

## Recommended .gitignore

```gitignore
# Tectonic output
*.pdf

# Keep intermediates (if using --keep-intermediates)
*.aux
*.bbl
*.bcf
*.blg
*.log
*.out
*.run.xml
*.toc
*.lot
*.lof
*.synctex.gz

# Tectonic cache (don't commit)
.tectonic/
```

## Compilation Workflow

### Development
```bash
# Quick compile during writing
tectonic manuscript.tex
```

### Debugging
```bash
# Verbose with intermediates
tectonic --keep-intermediates -X compile manuscript.tex
```

### CI/CD
```bash
# Specify exact bundle for reproducibility
tectonic --bundle https://data1.fullyjustified.net/tlextras-2023.1r0.tar manuscript.tex
```

## Figure Handling

- Use PDF or PNG for figures (wide support)
- EPS files work but get converted
- SVG requires additional packages (not always available)

## Multi-file Documents

Use `\input{}` or `\include{}` as normal:
```latex
\input{sections/introduction}
\input{sections/methods}
```

Tectonic handles dependencies automatically.

## Tips

1. **Cache is your friend** - First compile downloads packages, subsequent compiles are fast
2. **Pin your bundle** - For reproducible builds in papers/theses
3. **Check compatibility first** - Not all TeX Live packages are in the bundle
4. **Use PDF output** - It's the default and most reliable
