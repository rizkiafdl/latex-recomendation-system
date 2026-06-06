# Word → LaTeX Migration

## Source file
`.context/raw/Formated Version 20251220.docx` — the authoritative formatted thesis draft as of 2025-12-20.

## What was migrated

Pandoc was run first to produce `raw_output.tex` (2567 lines) and extract embedded images to `extracted-images/media/`. A Python transformation script then converted the pandoc output into the BINUS thesis structure.

| File | Before | After | Source lines in raw_output.tex |
|---|---|---|---|
| `bab1.tex` | 79 lines (lipsum stubs) | 150 lines | 352–517 |
| `bab2.tex` | 49 lines (lipsum stubs) | 417 lines | 518–1021 |
| `bab3.tex` | 18 lines (lipsum stubs) | 984 lines | 1021–2075 |
| `bab4.tex` | 209 lines | **unchanged** | 2076–2472 (too unstructured) |
| `bab5.tex` | 12 lines | **unchanged** | not written in docx yet |

BAB 4 in the docx had section headings written as plain body text (not Word Heading styles), so the pandoc output was unstructured prose. The existing `bab4.tex` (with proper figure/table structure for the UI screenshots) was kept as-is.

## Transformations applied

The Python migration script did the following to each extracted chapter:

| Pandoc output | → BINUS LaTeX |
|---|---|
| `\section{BAB N}`, `\section{CHAPTER TITLE}`, `\section{}` | deleted (chapter heading comes from `\chapter{\babN}` header) |
| `\subsection{...}` | `\section{...}` |
| `\subsubsection{...}` | `\subsection{...}` |
| `\paragraph{...}` | `\subsection{...}` (level 4 promoted to 3 — acceptable for thesis) |
| `\begin{quote}...\end{quote}` | stripped (Word body-text styles mapped to blockquote by pandoc) |
| `{\def\LTcaptype{none}` wrapper around longtables | stripped (let longtable increment the counter normally) |
| `\protect\phantomsection\label{_TocNNN}{}` | removed (TOC bookmarks, not needed in LaTeX) |
| `\texorpdfstring{ Text}{ Text}` | unwrapped to plain `Text` |
| `\hl{text}` | `\textbf{text}` (soul package not added) |
| `\ul{text}` | `\underline{text}` |
| `\pandocbounded{\includegraphics{...}}` | `\includegraphics{...}` |
| `./extracted-images/media/imageN.png` | `pic/imageN` |
| `alt={...}` in `\includegraphics` options | removed (not valid LaTeX) |

## Packages added to Skripsi.tex

```latex
\usepackage{longtable}   % for tables spanning multiple pages (pandoc output)
\usepackage{booktabs}    % for \toprule, \midrule, \bottomrule in longtables
```

Added directly after `\usepackage{ta}`.

## Image mapping

All images extracted from the docx and placed in `pic/`:

| File in `pic/` | Source in docx | Used in |
|---|---|---|
| `logo.png` | image1.jpeg | `sampul.tex`, `sampul2.tex` (pre-existing) |
| *(front matter images)* | image2–6.png | Signature pages — not used in chapter files |
| `kerangka-berpikir.png` | image7.png | `bab3.tex` line 9 (Gambar 3.1) |
| `usecase-diagram.png` | image8.png | `bab3.tex` line 345 (Gambar 3.2) |
| `image9.png` | image9.png | `bab3.tex` line 893 — Class Diagram (rename when confirmed) |
| `image10.png` | image10.png | `bab3.tex` line 904 — ERD (rename when confirmed) |
| `login.png` … `rulestudio.png` | image11–18.png | `bab4.tex` (pre-existing, already named) |

## Known issues / TODOs after migration

1. **Figure 1.1 missing** (`bab1.tex` line 15): The student-growth bar chart was a text box or chart object in Word, not an embedded image — pandoc couldn't extract it. A commented placeholder is left in `bab1.tex`. Add the actual image file to `pic/` and uncomment.

2. **`pic/image9.png` and `pic/image10.png`**: Still use generic names. Rename to meaningful names (e.g. `class-diagram.png`, `erd.png`) and update the `\includegraphics` references in `bab3.tex` lines 893 and 904.

3. **Citations use inline prose format**: bab2 and bab3 body text says things like "Ricci, Rokach, dan Shapira (2015) mendefinisikan..." — these are plain text, not `\cite{}` commands. All citation keys need to be added to `ref.bib` and replaced in the text with `\citep{key}` / `\citet{key}` as appropriate.

4. **Duplicate `\subsection{Analisis Permasalahan}`** in `bab3.tex` lines 29 and 33: Both headings came from the Word doc with the same text. The second one covers the actor analysis (EPC as admin, students/supervisors as data entities) — should be renamed, e.g. `\subsection{Analisis Aktor}`.

5. **BAB 5 empty**: The docx does not contain BAB 5 (Simpulan dan Saran) content yet. `bab5.tex` remains a lipsum stub.

6. **BAB 4 not migrated**: The docx BAB 4 content (sections 4.1 Testing Environment, 4.2 Black-box testing tables, 4.3 evaluation results) was written with plain-text headings in Word (not Heading styles), so pandoc produced unstructured output. The existing `bab4.tex` with proper figure/table structure was kept. BAB 4 content from `raw_output.tex` lines 2076–2472 can be manually ported if needed.

7. **Table captions missing**: All longtables in bab2 and bab3 came from the docx without Word captions, so they have no `\caption{}`. Add captions and `\label{table:tabN.M}` entries for tables that need to appear in the List of Tables.

## How to re-run the migration (if needed)

```bash
# Step 1: re-run pandoc (if docx changes)
cd /path/to/Skripsi_BINUS
pandoc ".context/raw/Formated Version 20251220.docx" \
  -o raw_output.tex \
  --extract-media=./extracted-images \
  --wrap=none

# Step 2: run migration script (the Python script was run inline;
#          recreate it from the transformation table above if needed)
```

The key transformation logic is documented in the "Transformations applied" table above — straightforward enough to reconstruct as a sed/Python script.
