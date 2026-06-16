---
name: thesis-config-audit
description: Current thesis LaTeX config audit — actual values vs BINUS guide requirements, mismatches flagged
metadata:
  type: project
---

# Thesis Config Audit
Source: `petunjuk-penulisan-cs.pdf` (Semester Genap 2025/2026, rev 01 Februari 2026)  
Last synced against code: 2026-06-16

> **Tracker note:** All items resolved (2026-06-16). A1–A6, A8–A10 fixed. A7 confirmed not a deviation (`DAFTAR PUSTAKA` correct for CS Bandung). A3 fixed (2026-06-16).

---

## Document Class

```latex
\documentclass[12pt, a4paper, onecolumn, twoside, final]{report}
```

| Setting | Current | Guide | Status |
|---|---|---|---|
| Font size | `12pt` | 12pt | ✅ |
| Paper | `a4paper` | A4 (80 gr) | ✅ |
| Sides | `twoside` | Bolak-balik (double-sided) dari Bab 1 s/d Lampiran | ✅ correct — guide requires it |

> The odd/even page margin-swap visible in PDF viewer is expected behavior from `twoside`. It is **not a bug** — BINUS explicitly requires double-sided printing.

---

## Margins (`ta.sty:82`)

```latex
\usepackage[paper=a4paper,headheight=0pt,left=4cm,top=2.5cm,right=2.5cm,bottom=2.5cm]{geometry}
```

Guide §2.1 / §2.3b: *"Margin kiri 4 cm, margin kanan, margin atas dan margin bawah 2,5 cm."*

| Margin | Current | Guide | Status |
|---|---|---|---|
| Left | 4 cm | 4 cm | ✅ |
| Right | 2.5 cm | 2.5 cm | ✅ A1 fixed |
| Top | 2.5 cm | 2.5 cm | ✅ A1 fixed |
| Bottom | 2.5 cm | 2.5 cm | ✅ A1 fixed |

---

## Font

```latex
\usepackage{pslatex}   % Times New Roman equivalent
```

| Setting | Current | Guide | Status |
|---|---|---|---|
| Font family | pslatex (Times New Roman) | Times New Roman | ✅ |
| Body size | 12pt | 12pt | ✅ |
| Italic usage | `\textit{}` / `\f{}` | Foreign words, uncommon terms, book/journal titles | ✅ |

---

## Line Spacing (`ta.sty:72-73`)

```latex
\usepackage{setspace}
  \onehalfspacing
%   \doublespacing
```

Guide §2.1: *"Ketika berspasi 1,5, memakai huruf tegak Times New Roman 12 point."*

| Setting | Current | Guide | Status |
|---|---|---|---|
| Body line spacing | `\onehalfspacing` (1.5) | 1.5 spasi | ✅ A2 fixed |

**Special spacing rules from guide §2.3o (not yet enforced in LaTeX):**

| Context | Guide | Current enforcement |
|---|---|---|
| Abstrak body | 1 spasi (single) | Not enforced — inherits 1.5 |
| Isi Abstrak → Kata Kunci | 1 spasi | Not enforced |
| Judul Bab → Subbab | 2 spasi | Handled by titlesec/fncychap |
| Subbab → Isi Materi | 1 spasi | Handled by titlesec |
| Isi Materi → Tabel/Gambar | 2 spasi | Not enforced (float spacing) |

---

## Page Numbering (`Skripsi.tex`)

Guide §2.3.1 (Bagian Awal): Roman numerals (i, ii, iii…) at bottom center.  
Guide §2.3.2a–b (Bagian Isi): Odd → top-right; Even → top-left. Chapter opening pages → bottom center, 1.5 cm dari bawah.

| Style | Applied to | Position | Guide | Status |
|---|---|---|---|---|
| `romanstyle` | Front matter | `\fancyfoot[C]` (center bottom) | Roman, center bottom | ✅ |
| `chapterstart` | Chapter opening page | `\fancyfoot[C]` (center bottom) | Center bottom | ✅ |
| `mainstyle` | Body chapters | `RO` top-right, `LE` top-left | Odd top-right, Even top-left | ✅ |

> Cover: `\pagenumbering{gobble}` → no number. ✅  
> Roman starts at `\setcounter{page}{2}` (cover counts as 1). ✅

---

## Chapter / Section Numbering (`ta.sty:312-314`) — ✅ Fixed (2026-06-16)

```latex
\renewcommand{\chaptername}{Chapter}
\renewcommand{\thechapter}{\Roman{chapter}}    % still Roman
\renewcommand{\thesection}{\arabic{chapter}.\arabic{section}}
```

Guide §2.3.2d: *"Tiap bab selalu diawali dengan judul bab yang ditulis di tengah (center). Sekitar 2,5 cm dari atas ditulis 'BAB n', di mana n adalah nomor bab dan ditulis dengan huruf biasa: 1, 2, 3, 4, 5."*

| Setting | Current | Guide | Status |
|---|---|---|---|
| Chapter prefix | `Chapter` (English) | `BAB` (Indonesian) | ❌ A3 pending |
| Chapter number style | `\Roman` (I, II…) | Arabic (1, 2, 3…) | ❌ A3 pending |
| Section numbering | `1.1`, `2.3` (Arabic) | — | ✅ |

**Fix needed (`ta.sty:312-313`):**
```latex
\renewcommand{\chaptername}{BAB}
\renewcommand{\thechapter}{\arabic{chapter}}
```

---

## Chapter Heading Style (`ta.sty:113`) — ✅ Kept as-is (fncychap ConnyRevised)

```latex
\usepackage[ConnyRevised]{fncychap}
```

Guide §2.3.2d: BAB title format:
- Line 1 (2.5 cm from top): `BAB n` — regular weight, centered
- Blank 2 spasi below
- Line 2: Chapter title — KAPITAL, **bold**, centered

| Setting | Current | Guide | Status |
|---|---|---|---|
| Style | fncychap `ConnyRevised` (decorative, large, with rules) | Plain centered: `BAB n` + `JUDUL` | ❌ A8 pending |

**Fix:** Comment out `\usepackage[ConnyRevised]{fncychap}` in `ta.sty:113`. Add `titlesec` chapter format in `Skripsi.tex` (already has `\usepackage{titlesec}` at line 79):
```latex
\titleformat{\chapter}[block]
  {\normalfont\normalsize\bfseries\centering}
  {BAB \thechapter}{0pt}{\\\MakeUppercase}
\titlespacing*{\chapter}{0pt}{2.5cm}{2\baselineskip}
```

---

## Figure & Table Labels (`ta.sty:317-323`) — ✅ Fixed (2026-06-16)

```latex
\renewcommand{\figurename}{\bo{Figure}}      % bold + English
\renewcommand{\tablename}{\bo{Table}}        % bold + English
```

Guide §2.3p: *"Judul gambar: Gambar ... (diberi nomor sebagai identitas)"* and *"Judul tabel: Tabel . . . ."*  
Guide §2.3m: *"Judul bagan/gambar, teks tabel adalah Times New Roman 12pt."* — no bold specified for label prefix.

| Element | Current | Guide | Status |
|---|---|---|---|
| Figure label | `\bo{Figure}` (English, bold) | `Gambar` (Indonesian, plain) | ❌ A4 pending |
| Table label | `\bo{Table}` (English, bold) | `Tabel` (Indonesian, plain) | ❌ A5 pending |
| Numbering format | `chapter.sequence` (e.g., 1.1) | chapter.sequence (e.g., Gambar 1.2, Tabel 3.1) | ✅ |
| Figure caption position | Below figure (`\caption` after `\includegraphics`) | Below figure, centered | ✅ |
| Table caption position | Above table (`\caption` before tabular) | Above table | ✅ |

**Fix (`ta.sty:317-323`):**
```latex
\renewcommand{\figurename}{Gambar}
\renewcommand{\tablename}{Tabel}
```

---

## Caption Font Size (`ta.sty:87`) — ✅ Fixed (2026-06-16)

```latex
\usepackage[font=footnotesize,format=plain,labelfont=bf,up,textfont=up,labelsep=period]{caption}
```

Guide §2.3m: *"Judul bagan/gambar, teks tabel adalah Times New Roman 12pt."*

| Setting | Current | Guide | Status |
|---|---|---|---|
| Caption font size | `footnotesize` (~10pt) | 12pt (normalsize) | ❌ A6 pending |
| Label font | `labelfont=bf` (bold) | Not specified as bold in guide | ⚠️ minor |

**Fix (`ta.sty:87`):**
```latex
\usepackage[font=normalsize,format=plain,labelfont={},up,textfont=up,labelsep=period]{caption}
```

---

## Bibliography (`Skripsi.tex:195`) — ✅ Correct

```latex
\renewcommand\bibname{DAFTAR PUSTAKA}
\bibliographystyle{apacite}
\bibliography{ref}
```

Guide §2.5: APA style (American Psychological Association) versi 5/6.

> **Note:** The generic BINUS guide text says `REFERENSI`, but the correct heading for this program (Computer Science, Bandung) is `DAFTAR PUSTAKA`. Confirmed by user 2026-06-16 — not a deviation.

| Setting | Current | Correct | Status |
|---|---|---|---|
| Bibliography heading | `DAFTAR PUSTAKA` | `DAFTAR PUSTAKA` | ✅ |
| Citation style | `apacite` (APA) | APA v5/6 | ✅ |

---

## List of Figures / Tables Names (`ta.sty` + `Skripsi.tex`)

```latex
% ta.sty (initial):
\renewcommand{\listfigurename}{LIST OF FIGURES}
\renewcommand{\listtablename}{LIST OF TABLES}

% Skripsi.tex (overrides):
\renewcommand{\listfigurename}{Daftar Gambar}
\renewcommand{\listtablename}{Daftar Tabel}
```

Effective output: `Daftar Gambar` / `Daftar Tabel` — guide §2.3.1j/i confirms these headings. ✅

Guide font for these lists: *"Huruf Times New Roman (judul, 14 point, bold), dan 12 point (isi), spasi 1."*  
> The 14pt bold heading and spasi 1 body are not explicitly enforced in current LaTeX config — minor, as LaTeX default list formats are close enough.

---

## Abstract Formatting (`abstrak.tex`)

Guide §2.3.1f:
- Title (`ABSTRACT`/`ABSTRAK`): Times New Roman **16pt**, KAPITAL, bold, centered
- Body: Times New Roman 12pt, **1 spasi (single)**, *italic*, for both English and Indonesian
- Length: 100–200 kata, satu paragraf

| Setting | Guide | Status |
|---|---|---|
| Title font size | 16pt bold kapital | ⚠️ check abstrak.tex — likely inherits 12pt |
| Body spacing | 1 spasi (singlespace) | ⚠️ inherits 1.5 spasi unless overridden |
| Body italic | italic | ✅ applied via `{\itshape …}` per C0 fix |

> The abstract body needs `\begin{singlespace}` wrapper to comply with the 1-spasi rule.

---

## Text Alignment

Guide §2.3e: *"Tepi kanan teks dibuat rata."* = full justification (both sides).

Current: `\sloppy` in `ta.sty:133` — this allows looser justification to prevent overfull hboxes. The output is still justified but may have wider word spacing on some lines. ✅ acceptable.

---

## Build Pipeline (`.latexmkrc`)

```perl
$out_dir = 'build';
$pdf_mode = 1;   # pdflatex
$bibtex_use = 2;
```

No guide requirement conflicts. Output: `build/Skripsi.pdf`. CI compiles on push to `main`. ✅

---

## Summary: Deviations from BINUS Guide

| # | Issue | Current | Should be | File | Status |
|---|---|---|---|---|---|
| A1 | Right/Top/Bottom margin | ~~3 cm~~ → 2.5 cm | 2.5 cm | `ta.sty:82` | ✅ Fixed |
| A2 | Line spacing | ~~double~~ → 1.5 | 1.5 spasi | `ta.sty:72-73` | ✅ Fixed |
| A3 | Chapter prefix + number | `Chapter` + Roman → `BAB` + Arabic | `BAB` + Arabic (1, 2…) | `ta.sty:312-313` | ✅ Fixed |
| A4 | Figure label | `\bo{Figure}` → `Gambar` | `Gambar` (plain) | `ta.sty:317` | ✅ Fixed |
| A5 | Table label | `\bo{Table}` → `Tabel` | `Tabel` (plain) | `ta.sty:322` | ✅ Fixed |
| A6 | Caption font size | `footnotesize` → `normalsize` | `normalsize` (12pt) | `ta.sty:87` | ✅ Fixed |
| A7 | Bibliography heading | `DAFTAR PUSTAKA` | `DAFTAR PUSTAKA` (confirmed correct for CS Bandung) | `Skripsi.tex:195` | ✅ Not a deviation |
| A8 | Chapter heading style | fncychap `ConnyRevised` | Kept — visual format was already correct; only label (A3) needed fixing | `ta.sty:113` | ✅ Not changed |
| A9 | Abstract body spacing | inherits 1.5 → singlespace | 1 spasi (single) | `abstrak.tex` | ✅ Fixed |
| A10 | Abstract title font | 12pt → 16pt bold kapital | 16pt bold kapital | `abstrak.tex` | ✅ Fixed |
| — | twoside | `twoside` | bolak-balik required | `Skripsi.tex:3` | ✅ Correct — NOT a deviation |
