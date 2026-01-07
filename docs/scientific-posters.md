# Scientific Posters with Tectonic

Guide to creating large-format scientific posters using `tikzposter` and Tectonic.

## Quick Start

```bash
# Compile poster
tectonic poster.tex

# Generate PNG preview (requires poppler)
pdftoppm -png -r 75 -singlefile poster.pdf poster_preview
```

## Template Structure

### Document Class

```latex
% 48" x 36" Landscape poster, 20pt base font
\documentclass[20pt, paperwidth=48in, paperheight=36in, landscape]{tikzposter}
```

### Essential Packages

```latex
\usepackage{graphicx}
\usepackage{amsmath, amssymb}
\usepackage{enumitem}
\usepackage{xcolor}
\usepackage{tikz}
\usepackage{qrcode}
\usepackage{fontspec}  % For custom fonts
```

### Custom Fonts

Tectonic supports system fonts via `fontspec`. TeX Gyre Heros (Helvetica alternative) works well:

```bash
# Install font (macOS)
brew install --cask font-tex-gyre-heros
```

```latex
\setmainfont{texgyreheros}[
    Extension = .otf,
    UprightFont = *-regular,
    BoldFont = *-bold,
    ItalicFont = *-italic,
    BoldItalicFont = *-bolditalic
]
```

## Styling

### Color Theme

```latex
\definecolor{NYUviolet}{RGB}{87, 6, 140}

\usetheme{Default}
\definecolorstyle{WhiteStyle}{
    \definecolor{colorOne}{RGB}{87, 6, 140}
}{
    \colorlet{backgroundcolor}{white}
    \colorlet{framecolor}{NYUviolet}
    \colorlet{titlefgcolor}{black}
    \colorlet{titlebgcolor}{white}
    \colorlet{blocktitlebgcolor}{NYUviolet}
    \colorlet{blocktitlefgcolor}{white}
    \colorlet{blockbodybgcolor}{white}
    \colorlet{blockbodyfgcolor}{black}
}
\usecolorstyle{WhiteStyle}
```

### Borderless Title

To remove the title box outline and maximize space:

```latex
\definetitlestyle{MinimalTitle}{
    width=\paperwidth, linewidth=0pt,
    titletotopverticalspace=5mm,
    titletoblockverticalspace=10mm
}{
    % Empty = no border drawing
}
\usetitlestyle{MinimalTitle}

\settitle{
    \centering
    {\fontsize{90}{95}\selectfont\bfseries \@title}\\[0.3em]
    {\large\@author}\\[0.2em]
    {\normalsize\@institute}
}
```

### Clean Block Style

```latex
\defineblockstyle{CleanBlock}{
    titlewidthscale=1, bodywidthscale=1, titleleft,
    titleoffsetx=0pt, titleoffsety=0pt,
    bodyoffsetx=0pt, bodyoffsety=0pt,
    bodyverticalshift=0pt, roundedcorners=5, linewidth=2pt,
    titleinnersep=3mm, bodyinnersep=4mm
}{
    \begin{scope}[line width=\blocklinewidth, rounded corners=\blockroundedcorners]
        \ifBlockHasTitle
            \draw[fill=blockbodybgcolor, draw=framecolor]
                (blockbody.south west) rectangle (blocktitle.north east);
            \draw[fill=blocktitlebgcolor, draw=framecolor]
                (blocktitle.south west) rectangle (blocktitle.north east);
        \else
            \draw[fill=blockbodybgcolor, draw=framecolor]
                (blockbody.south west) rectangle (blockbody.north east);
        \fi
    \end{scope}
}
\useblockstyle{CleanBlock}
```

## Layout Tips

### Multi-Column Layout

```latex
\begin{columns}
\column{0.18}  % Column 1: 18% width
\block{Title}{Content}

\column{0.27}  % Column 2: 27% width
\block{Title}{Content}

\column{0.27}  % Column 3: 27% width
\block{Title}{Content}

\column{0.28}  % Column 4: 28% width
\block{Title}{Content}
\end{columns}
```

### Dense Text with Paragraph Format

Use paragraph format instead of bullet lists to save vertical space:

```latex
\textbf{Topic 1:} Description here. \textbf{Topic 2:} More description.
```

vs.

```latex
\begin{itemize}
    \item Topic 1: Description
    \item Topic 2: Description
\end{itemize}
```

### Equations

Equations work normally with `amsmath`:

```latex
\[
\hat{y} = \arg\max_{y \in \{\text{class1, class2}\}} p(y|x;\theta)
\]
```

### QR Codes

```latex
\usepackage{qrcode}
% ...
\qrcode[height=1in]{https://doi.org/10.1016/j.example.2025.123456}
```

## Visual Feedback Loop

For iterative design, use this workflow:

```bash
# Compile and preview in one command
tectonic poster.tex && pdftoppm -png -r 75 -singlefile poster.pdf poster_preview

# Then view poster_preview.png in your image viewer
```

The `-r 75` flag sets 75 DPI, producing a ~3600x2700px preview suitable for quick checks without generating massive files.

## Wrapping Text Around QR Codes / Images

Getting text to wrap tightly around QR codes or small images in tikzposter can be tricky. Here's what works and what doesn't:

### What Doesn't Work

**Inline with `\hfill`:**
```latex
\textbf{Funding:} NYUAD Research Funds.%
\hfill%
\begin{tabular}[t]{cc}
    \includegraphics[height=0.6in]{qrcode.png} \\
    \scriptsize Label
\end{tabular}
```
Result: Creates a large horizontal gap between text and QR code.

**Side-by-side minipages:**
```latex
\begin{minipage}[t]{0.6\linewidth}
    Text here...
\end{minipage}%
\hfill
\begin{minipage}[t]{0.35\linewidth}
    \centering
    \includegraphics[height=0.6in]{qrcode.png}
\end{minipage}
```
Result: Text doesn't wrap; leaves vertical whitespace.

**Centered tabular:**
```latex
\begin{center}
\begin{tabular}{cc}
    \includegraphics[height=0.6in]{qr1.png} &
    \includegraphics[height=0.6in]{qr2.png} \\
    \scriptsize Label 1 & \scriptsize Label 2
\end{tabular}
\end{center}
```
Result: QR codes centered with large vertical gaps above/below.

### What Works: `wrapfigure`

Add the package:
```latex
\usepackage{wrapfig}
```

Then use `wrapfigure` to float the QR codes to the right:
```latex
\begin{wrapfigure}{r}{1.6in}
\vspace{-12pt}
\centering
\begin{tabular}{@{}c@{\hspace{3pt}}c@{}}
    \includegraphics[height=0.6in]{qrcode_models.png} &
    \includegraphics[height=0.6in]{qrcode_paper.png} \\[-2pt]
    \scriptsize Models & \scriptsize Full Paper
\end{tabular}
\vspace{-10pt}
\end{wrapfigure}
\textbf{Open-Source Suite:} Description here...
\textbf{Data:} Zenodo link.
\textbf{Models:} Hugging Face.
\textbf{Code:} GitHub.
\textbf{Funding:} Grant info.
```

**Key points:**
- `{r}` = float to right side
- `{1.6in}` = width allocated for the wrapped content
- `\vspace{-12pt}` at top removes extra space above QR codes
- `\vspace{-10pt}` at bottom tightens space below
- `@{}` in tabular removes column padding
- `@{\hspace{3pt}}` adds minimal gap between QR codes
- `\\[-2pt]` reduces space between QR codes and labels
- Text following `\end{wrapfigure}` automatically wraps around

This produces tight text wrapping with QR codes floating to the right, labels underneath, and no wasted whitespace.

## Common Issues

### Content Overflow

If blocks extend past the artboard:
1. Reduce font size (`\small` or `\footnotesize`)
2. Convert bullet lists to paragraph format
3. Adjust column widths
4. Reduce `bodyinnersep` in block style

### Figure Sizing

```latex
\includegraphics[width=\linewidth]{figures/figure.pdf}
```

Using `\linewidth` ensures figures scale to column width automatically.

### PDF Version Warnings

```
warning: Trying to include PDF file with version (1.6), newer than output (1.5)
```

This warning is harmless; the PDF will render correctly.

### Missing Elements in PDF Figures (Colormap Legends, Transparency Issues)

Sometimes PDF figures exported from plotting libraries or Adobe Illustrator have missing elements (colormap legends, transparent overlays) when compiled with Tectonic. This is often due to PDF transparency or non-conforming PDF structure.

**Symptoms:**
- Colormap/colorbar legends disappear
- Transparent elements render incorrectly
- Figure appears correct in Preview.app but not in compiled poster

**Solution: Ghostscript + PNG Workaround**

The fix involves two steps: (1) reprocess with Ghostscript to fix transparency/font issues, then (2) render to PNG to avoid rotation problems that Ghostscript can introduce.

```bash
# Install tools (macOS)
brew install ghostscript poppler

# Step 1: Reprocess PDF with Ghostscript
gs -dNOPAUSE -dBATCH -sDEVICE=pdfwrite \
   -dCompatibilityLevel=1.5 \
   -dEmbedAllFonts=true \
   -dSubsetFonts=true \
   -dAutoRotatePages=/None \
   -dPDFSETTINGS=/prepress \
   -sOutputFile=figure-gs.pdf figure-original.pdf

# Step 2: Render to high-res PNG (Ghostscript often introduces rotation)
pdftoppm -png -r 300 -singlefile figure-gs.pdf figure-fixed
```

Then include the PNG instead of the PDF:
```latex
\includegraphics[width=\linewidth]{figures/figure-fixed.png}
```

**Why this works:**
- Ghostscript repairs non-conforming PDF structures and re-embeds fonts
- However, Ghostscript can change internal page rotation in ways that `xdvipdfmx` (Tectonic's PDF handler) interprets incorrectly
- Rendering to PNG "flattens" everything, bypassing rotation metadata issues
- 300 DPI is sufficient for large-format poster printing

**What doesn't work:**
- `qpdf --rotate` - only affects page-level rotation, not content matrix
- LaTeX `angle=90` parameter - sizing becomes problematic after rotation
- `-dAutoRotatePages=/None` alone - rotation is embedded in content stream

**Note:** This trades vector graphics for raster, which is acceptable for figures but not ideal for text-heavy content. For most scientific figures at 300 DPI on a 48" poster, quality is indistinguishable from vector.

## Example

See `examples/scientific-poster/` for a complete working example.
