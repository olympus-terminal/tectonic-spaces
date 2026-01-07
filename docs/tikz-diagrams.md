# TikZ Scientific Diagrams

Best practices and patterns for creating publication-quality TikZ diagrams with Tectonic.

## Typography Setup (6pt Arial)

```latex
\documentclass[tikz,border=2mm]{standalone}

% Typography - 6pt Arial
\usepackage{fontspec}
\setmainfont{Arial}[Scale=0.5]  % Base 12pt * 0.5 = 6pt
\setsansfont{Arial}[Scale=0.5]
\renewcommand{\familydefault}{\sfdefault}

% Math support
\usepackage{amsmath}
\usepackage{amssymb}
```

**Note**: Tectonic accesses system fonts (shows warning about `/System/Library/Fonts/` but works fine).

## Required TikZ Libraries

```latex
\usetikzlibrary{
    positioning,        % Relative node placement
    shapes.geometric,   % Rectangles, circles, etc.
    arrows.meta,        % Modern arrow tips
    calc,               % Coordinate calculations
    fit,                % Fitting nodes around others
    backgrounds         % Background layers
}
```

## Standard Styles (FIGURE_PROTOCOL.md compliant)

### Line Weight: 0.25pt

```latex
\begin{tikzpicture}[
    % Global
    every node/.style={font=\sffamily, text=textgray},

    % Main boxes - 0.25pt lines
    mainbox/.style={
        rectangle, rounded corners=1mm,
        draw=arrowcolor, line width=0.25pt,
        minimum width=65mm, minimum height=8mm,
        align=center, inner sep=1.5mm
    },

    % Smaller boxes for parallel elements
    smallbox/.style={
        rectangle, rounded corners=0.8mm,
        draw=arrowcolor, line width=0.25pt,
        minimum width=20mm, minimum height=18mm,
        align=center, inner sep=1.2mm
    },

    % Arrows
    myarrow/.style={
        -{Stealth[length=1.2mm, width=0.8mm]},
        line width=0.25pt, arrowcolor
    }
]
```

## Color Scheme (Scientific)

```latex
% Process flow colors
\definecolor{expcolor}{RGB}{255, 245, 235}     % Warm cream - experimental
\definecolor{proccolor}{RGB}{240, 245, 255}    % Light blue - processing
\definecolor{analycolor}{RGB}{245, 255, 245}   % Light green - analysis
\definecolor{resultcolor}{RGB}{255, 250, 240}  % Cream - results
\definecolor{arrowcolor}{RGB}{80, 80, 80}      % Dark gray arrows
\definecolor{textgray}{RGB}{40, 40, 40}        % Near-black text

% Treatment-specific (if needed)
\definecolor{treatment1}{RGB}{70, 130, 180}    % Steel blue
\definecolor{treatment2}{RGB}{178, 102, 102}   % Dusty red
```

## Common Patterns

### Vertical Flow with Arrows

```latex
\node[mainbox, fill=proccolor] (step1) {Step 1 content};
\node[mainbox, fill=proccolor, below=4mm of step1] (step2) {Step 2 content};
\draw[myarrow] (step1) -- (step2);
```

### Parallel Branches (3-way split)

```latex
% Main node above branches
\node[mainbox] (main) {Main processing};

% Three parallel branches
\node[smallbox, below left=8mm and 22mm of main] (branchA) {Branch A};
\node[smallbox, below=8mm of main] (branchB) {Branch B};
\node[smallbox, below right=8mm and 22mm of main] (branchC) {Branch C};

% Arrows splitting to branches
\draw[myarrow] (main.south) -- ++(0,-3mm) -| (branchA.north);
\draw[myarrow] (main.south) -- (branchB.north);
\draw[myarrow] (main.south) -- ++(0,-3mm) -| (branchC.north);
```

### Converging Branches

```latex
% Node below branches
\node[mainbox, below=26mm of branchB] (converge) {Convergence};

% Arrows from branches
\draw[myarrow] (branchA.south) -- ++(0,-3mm) -| (converge.north west);
\draw[myarrow] (branchB.south) -- (converge.north);
\draw[myarrow] (branchC.south) -- ++(0,-3mm) -| (converge.north east);
```

### Tables Inside Nodes

```latex
\node[smallbox] (stats) {
    \textbf{Title}\\[1mm]
    \begin{tabular}{@{}l@{}}
    Row 1: value\\
    Row 2: value\\
    Row 3: value
    \end{tabular}
};
```

### Side Annotations

```latex
\node[anchor=north west, align=left, font=\sffamily\tiny]
    at ([xshift=2mm, yshift=-1mm]mainnode.north east) {
    \textbf{Annotation Title}\\
    Detail 1\\
    Detail 2
};
```

## Math in TikZ (with amsmath)

```latex
% Absolute value
\(\lvert x \rvert\)

% Subscripts/superscripts
log\textsubscript{2}FC
10\textsuperscript{-21}

% Greek letters
\(\Delta\)PSI
NF-\(\kappa\)B

% Inequalities
\(\geq\) 1
\(<\) 0.05
```

## Compilation

```bash
cd figures/_generated
tectonic methodology_diagram.tex
```

Tectonic handles:
- Font embedding automatically
- Multiple passes for references
- PDF output with proper font types

## Validation Checklist

- [ ] All text 6pt (Scale=0.5 on 12pt base)
- [ ] Line weights 0.25pt
- [ ] No text overlap (check at 400% zoom)
- [ ] Colors are subtle, not saturated
- [ ] Arrows are thin with small heads
- [ ] Vector PDF output

## Example: CROCIN-HCC Methodology Diagram

Full working example at:
`/Users/drn2/Documents/CROCIN_2023/figures/_generated/methodology_diagram.tex`

Features:
- 6-layer vertical flow
- 3 parallel analysis branches
- Technical annotations (thresholds, gene counts)
- Color-coded sections

---

## Lessons Learned: Iterative Refinement

### What Worked

1. **Start simple, add complexity gradually**
   - Begin with basic structure (boxes + arrows)
   - Add content incrementally
   - Test after each change

2. **Equal-height boxes for parallel elements**
   ```latex
   \node[box, minimum height=11mm, minimum width=22mm, anchor=north] ...
   ```
   - Use `anchor=north` to align tops
   - Explicit `minimum height` prevents uneven rows

3. **Single split point for branching arrows**
   ```latex
   \coordinate (split) at ($(parent.south)+(0,-2mm)$);
   \draw[arr] (parent.south) -- (split) -| (childA.north);
   \draw[arr] (split) -- (childB.north);
   \draw[arr] (split) -| (childC.north);
   ```
   - Cleaner than three separate arrows from parent

4. **Diagonal converging arrows**
   ```latex
   \draw[arr] (branchA.south) -- ($(target.north west)+(2mm,0)$);
   ```
   - Avoids awkward L-shaped paths
   - More visually appealing than `|-` or `-|`

### What Failed

1. **Adding too much detail at once**
   - Temporal cascade + splicing details + dysregulated pathways = FUBAR
   - Layout became sprawling and unmanageable
   - Elements overlapped, text got clipped

2. **Automated agents making too many changes**
   - 8 iterations without human review = drift
   - Each "fix" introduced new problems
   - Lost sight of overall layout coherence

3. **Complex nested structures**
   - Tables inside nodes often overflow
   - `text width` constraints cause clipping
   - Better to keep node content simple

### Best Practices

1. **One change at a time** - compile and view after each edit
2. **Keep content minimal** - details go in manuscript, not diagram
3. **Test arrow paths visually** - what looks good in code may overlap
4. **Use explicit spacing** - `below=3mm` not `below` (default varies)
5. **Balance up/down content** - don't just show what's downregulated
6. **Revert early** - if layout breaks, go back to last working version

### Spacing Guidelines (CROCIN diagram)

| Element | Spacing |
|---------|---------|
| Between linear flow boxes | 3mm |
| Before branch split | 3.5mm + 2mm coordinate drop |
| Between branches (horizontal) | 24mm from center |
| Before converging target | 3mm |
| Arrow head clearance | ~2mm minimum |

### Color Scheme

```latex
\definecolor{expcolor}{RGB}{255, 248, 240}    % Warm cream - experimental
\definecolor{proccolor}{RGB}{235, 242, 255}   % Light blue - processing
\definecolor{analycolor}{RGB}{240, 252, 240}  % Light green - analysis
\definecolor{resultcolor}{RGB}{255, 250, 242} % Cream - results
```
