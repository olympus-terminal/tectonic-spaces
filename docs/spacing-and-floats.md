# LaTeX/Tectonic Spacing Fix Documentation

**Date:** 2026-01-05
**Issue:** Large empty spaces appearing in manuscript after compilation with tectonic

## Problem Description

After compilation with tectonic, large vertical gaps appeared between content elements:
1. After Table 1 on page 3
2. Giant gap on page 9 before IPA figures
3. Large spaces below figures throughout the document

## Root Causes Identified

### Cause 1: Strict Float Placement `[H]`
The original manuscript used `[H]` float specifier (from the `float` package), which forces exact placement. When a float is too large to fit in the remaining page space, LaTeX cannot place it and leaves a gap.

### Cause 2: Full-Page Figure PDFs
Several figures were exported as full US Letter page size (8.5 x 11 inches) with internal whitespace:
- `Fig3_Splicing_Overview_Compact.pdf`: Was 8.5 x 11 in (converted from SVG via Chrome)
- `Figure_3-IPA.1.pdf`: 8.5 x 11 in (IPA export with wrong bounding boxes)
- `Figure_4-IPA.2.pdf`: 8.5 x 11 in (IPA export with wrong bounding boxes)

When scaled to fit `width=0.9\textwidth`, the internal whitespace caused large gaps.

### Cause 3: `placeins` Package with `[section]` Option (KEY FIX)
The `\usepackage[section]{placeins}` option inserts hidden `\FloatBarrier` commands at every `\section` and `\subsection` boundary. This prevented floats from flowing naturally across section boundaries, causing large gaps when figures couldn't fit before a section break.

**This was the primary cause of the giant gap on page 9.**

## Solutions Applied

### Fix 1: Float Spacing Controls
Added to preamble (after `\raggedbottom`):
```latex
\setlength{\floatsep}{8pt plus 2pt minus 2pt}
\setlength{\textfloatsep}{10pt plus 2pt minus 4pt}
\setlength{\intextsep}{8pt plus 2pt minus 2pt}
\setlength{\abovecaptionskip}{6pt}
\setlength{\belowcaptionskip}{2pt}
```

### Fix 2: Removed `[section]` from Placeins (CRITICAL)
Changed from:
```latex
\usepackage[section]{placeins}  % BAD: inserts hidden FloatBarriers
```
To:
```latex
\usepackage{placeins}  % GOOD: only provides manual \FloatBarrier command
```

**Why this matters:** The `[section]` option was silently inserting `\FloatBarrier` at every section boundary, trapping floats and creating gaps. Without this option, floats can flow naturally while `\FloatBarrier` remains available for manual use when needed.

### Fix 3: Changed Float Specifiers `[H]` → `[!htb]`
Changed all figure/table specifiers:
- `!` = Override internal LaTeX parameters (be aggressive)
- `h` = Try to place "here" first
- `t` = If not here, place at top of page
- `b` = If not top, place at bottom of page

**Do NOT use `[H]`** - it forces exact placement and causes gaps when figures don't fit.

### Fix 4: Regenerated Matplotlib Figures
Re-ran `create_splicing_multipanel_20260104_105500.py` to generate properly-sized PDFs:
- **Before:** 8.5 x 11.0 in (full page with whitespace)
- **After:** 6.2 x 5.0 in (tight bounds)

The Python script uses `bbox_inches='tight'` in `savefig()` to eliminate whitespace.

### Fix 5: Fixed IPA Figure Bounding Boxes
The IPA-exported PDFs had correct visual content but wrong PDF bounding boxes (MediaBox, CropBox, TrimBox, ArtBox all set to full page).

**Solution:** Re-export from Adobe Illustrator with proper artboard sizing:
1. Open PDF in Illustrator
2. Select all content → Object → Artboards → Fit to Selected Art
3. Save as PDF (ensures bounding boxes match content)

**Verification command:**
```bash
python3 -c "from pypdf import PdfReader; r=PdfReader('file.pdf'); print(r.pages[0].mediabox)"
```

### Fix 6: Position Figures After First Mention
Moved figure environments to appear immediately after the paragraph containing their first `\ref{}` citation. This keeps figures close to their discussion while allowing `[!htb]` flexibility.

### Fix 7: Flatten Transparency for Illustrator PDFs (CRITICAL for tectonic)
**Problem:** PDFs exported from Adobe Illustrator may render perfectly in Preview but have missing elements (scatter points, heatmap colors, etc.) after tectonic compilation. This occurs because tectonic's PDF backend (xdvipdfmx) cannot properly handle certain transparency and blend mode features.

**Symptoms:**
- PDF looks perfect in Preview/Acrobat
- After tectonic compilation, elements are missing (especially dense scatter plots, gradient fills)
- PDF version warnings may or may not appear

**Solution:** When exporting PDF from Illustrator, flatten transparency:

1. **File → Save As → PDF**
2. In the PDF dialog, go to **Advanced** tab
3. Set **Transparency Flattener** preset to `[High Resolution]`
4. Optionally set Compatibility to `Acrobat 5 (PDF 1.4)` for maximum compatibility

**Important:** This flattens transparency only in the exported PDF. Your `.ai` source file remains fully editable with all transparency intact. Always keep your `.ai` files as the editable source.

**Alternative methods if Illustrator export still fails:**
1. **Preview Print-to-PDF:** Open PDF in Preview → File → Print → PDF dropdown → Save as PDF (completely flattens)
2. **Ghostscript:** `gs -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -sOutputFile=out.pdf in.pdf`

## Files Modified

- `manuscript_article.tex` - LaTeX settings, float specifiers, figure positions
- `figures/Fig3_Splicing_Overview_Compact.pdf` - Regenerated with tight bounds
- `figures/Fig4_Spliceosome_Compact.pdf` - Regenerated with tight bounds
- `figures/Figure_3-IPA.1.pdf` - Re-exported with correct bounding boxes
- `figures/Figure_4-IPA.2.pdf` - Re-exported with correct bounding boxes

## Key Lessons

1. **Never use `\usepackage[section]{placeins}`** - it inserts hidden barriers that cause gaps. Use `\usepackage{placeins}` and add manual `\FloatBarrier` only where needed.

2. **Never use `[H]` float specifier** - it's too strict and causes gaps. Use `[!htb]` for flexibility.

3. **SVG to PDF conversion via Chrome** creates full-page PDFs. Use matplotlib's `savefig(bbox_inches='tight')` instead.

4. **IPA/Illustrator exports** may have visual content tight but bounding boxes set to full page. Always verify with pypdf and re-export with "Fit to Artboard" if needed.

5. **Check PDF dimensions** before including in LaTeX:
   ```bash
   python3 << 'EOF'
   from pypdf import PdfReader
   r = PdfReader('file.pdf')
   box = r.pages[0].mediabox
   print(f"{float(box.width)/72:.1f} x {float(box.height)/72:.1f} inches")
   EOF
   ```

6. **Always flatten transparency** when exporting PDFs from Illustrator for tectonic. The PDF may look perfect in Preview but fail to render correctly in tectonic's xdvipdfmx backend. Use Illustrator's "Transparency Flattener" set to High Resolution in the PDF export dialog.

## Summary of Preamble Settings

```latex
% Prevent large blank spaces
\raggedbottom

% Reduce float spacing
\setlength{\floatsep}{8pt plus 2pt minus 2pt}
\setlength{\textfloatsep}{10pt plus 2pt minus 4pt}
\setlength{\intextsep}{8pt plus 2pt minus 2pt}
\setlength{\abovecaptionskip}{6pt}
\setlength{\belowcaptionskip}{2pt}

% Float handling - NO [section] option!
\usepackage{float}
\usepackage{placeins}
```

## Float Specifier Reference

| Specifier | Meaning |
|-----------|---------|
| `[H]` | Force here (AVOID - causes gaps) |
| `[h]` | Try here |
| `[t]` | Top of page |
| `[b]` | Bottom of page |
| `[p]` | Dedicated float page |
| `[!]` | Override restrictions |
| `[!htb]` | **RECOMMENDED** - aggressive here/top/bottom |
