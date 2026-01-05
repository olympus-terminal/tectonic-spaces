# Workflow Tips

## Editor Integration

### VS Code
- Use LaTeX Workshop extension
- Configure to use tectonic as the builder:
```json
{
  "latex-workshop.latex.tools": [
    {
      "name": "tectonic",
      "command": "tectonic",
      "args": ["%DOC%.tex"]
    }
  ],
  "latex-workshop.latex.recipes": [
    {
      "name": "tectonic",
      "tools": ["tectonic"]
    }
  ]
}
```

### Vim/Neovim
- vimtex works with tectonic
- Set `let g:vimtex_compiler_method = 'tectonic'`

## Watch Mode (Auto-recompile)

Tectonic doesn't have built-in watch. Use external tools:

```bash
# Using fswatch (macOS)
fswatch -o *.tex | xargs -n1 -I{} tectonic manuscript.tex

# Using entr
ls *.tex *.bib | entr tectonic manuscript.tex

# Using watchexec
watchexec -e tex,bib -- tectonic manuscript.tex
```

## Makefile Example

```makefile
TEX = manuscript.tex
PDF = $(TEX:.tex=.pdf)

.PHONY: all clean watch

all: $(PDF)

$(PDF): $(TEX) references.bib
	tectonic $(TEX)

clean:
	rm -f *.aux *.bbl *.bcf *.blg *.log *.out *.run.xml

watch:
	watchexec -e tex,bib -- tectonic $(TEX)
```

## Continuous Integration

### GitHub Actions
```yaml
name: Build PDF
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: wtfjoke/setup-tectonic@v3
      - run: tectonic manuscript.tex
      - uses: actions/upload-artifact@v4
        with:
          name: manuscript
          path: manuscript.pdf
```

## Diff / Track Changes

For manuscript revisions with tracked changes:
```bash
# Using latexdiff (if installed separately)
latexdiff old.tex new.tex > diff.tex
tectonic diff.tex
```

## Quick Debug Checklist

1. [ ] Is the .tex file syntax correct?
2. [ ] Are all `\input` files present?
3. [ ] Is the .bib file valid?
4. [ ] Try `--keep-intermediates` to inspect .log
5. [ ] Clear cache: `rm -rf ~/.cache/Tectonic`
6. [ ] Try verbose: `tectonic -X compile doc.tex`
