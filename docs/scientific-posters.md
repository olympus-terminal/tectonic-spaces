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

## Example

See `examples/scientific-poster/` for a complete working example.
