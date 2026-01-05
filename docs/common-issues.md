# Common Issues & Solutions

## Package Download Failures

### Problem
Tectonic fails to download packages, especially on first run or with unstable connections.

### Solutions
```bash
# Retry with verbose output to see what's failing
tectonic -X compile document.tex

# Use a different bundle mirror
tectonic --bundle https://data1.fullyjustified.net/tlextras-2023.1r0.tar document.tex
```

---

## Missing Fonts

### Problem
```
error: xdvipdfmx: Cannot proceed without .vf or "physical" font for...
```

### Solutions
- Check if the font package is supported
- Try alternative fonts that ship with tectonic's bundle
- For system fonts, may need to configure fontspec differently

---

## BibTeX/Biber Issues

### Problem
Bibliography not generating or references showing as `[?]`

### Solutions
Tectonic handles bibliography automatically, but:
```bash
# If issues persist, try cleaning cache
rm -rf ~/.cache/Tectonic

# Check .bib file syntax
```

---

## Auxiliary Files

### Problem
Need to inspect `.aux`, `.log`, or other intermediate files for debugging

### Solution
```bash
tectonic --keep-intermediates document.tex
```

---

## "Too many iterations" Error

### Problem
Document references don't stabilize

### Solution
Check for:
- Circular references
- Packages that modify page layout dynamically
- Consider `--reruns N` flag if legitimately complex

---

## Add Your Issues Below

<!-- Document new issues as you encounter them -->
