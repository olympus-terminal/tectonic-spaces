# Scientific Poster Example

48" x 36" landscape research poster created with `tikzposter` and compiled with Tectonic.

## Features Demonstrated

- **Custom fonts**: TeX Gyre Heros via `fontspec`
- **Borderless title**: Custom `\definetitlestyle` with no frame
- **Clean block style**: Rounded corners, NYU violet accent color
- **Multi-column layout**: 4 columns with varying widths
- **Math equations**: Decision rules, attention mechanisms, matrix operations
- **QR code**: Links to paper DOI
- **Figure inclusion**: PDF figures scaled to column width

## Compilation

```bash
# Compile
tectonic poster.tex

# Generate preview PNG
pdftoppm -png -r 75 -singlefile poster.pdf poster_preview
```

## Requirements

- Tectonic (tested with 0.15.0)
- TeX Gyre Heros font: `brew install --cask font-tex-gyre-heros`
- poppler for PNG preview: `brew install poppler`

## Structure

```
scientific-poster/
├── poster.tex          # Main document
├── figures/            # PDF figures
│   ├── FIGURE_1-*.pdf
│   ├── FIGURE_2-*.pdf
│   └── ...
└── README.md
```

## Key Techniques

### Paragraph Format for Dense Content

Instead of bullet lists, use flowing paragraphs with bold labels:

```latex
\textbf{Dataset:} 77M sequences from 166 genomes. \textbf{Training:} Mixed-precision FP16...
```

### Visual Feedback Loop

Rapidly iterate by compiling and viewing:

```bash
tectonic poster.tex && pdftoppm -png -r 75 -singlefile poster.pdf poster_preview && open poster_preview.png
```
