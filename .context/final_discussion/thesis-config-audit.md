---
name: thesis-config-audit
description: Current thesis LaTeX config audit — actual values vs BINUS guide requirements, mismatches flagged
metadata:
  type: project
---

# Thesis Config Audit
Crawled: 2026-06-14  
Source files: `Skripsi.tex`, `ta.sty` / `Awal_konfigurasi.tex`, `.latexmkrc`

---

## Document Class

```latex
\documentclass[12pt, a4paper, onecolumn, twoside, final]{report}
```

| Setting | Current | BINUS Guide | Status |
|---|---|---|---|
| Base class | `report` | — | — |
| Font size | `12pt` | 12pt | ✅ |
| Paper | `a4paper` | A4 | ✅ |
| Sides | `twoside` | Double-sided (bolak-balik) | ✅ |

---

## Margins (`geometry` in `Awal_konfigurasi.tex`)

```latex
\usepackage[paper=a4paper, headheight=0pt, left=4cm, top=3cm, right=3cm, bottom=3cm]{geometry}
```

| Margin | Current | BINUS Guide | Status |
|---|---|---|---|
| Left | 4 cm | 4 cm | ✅ |
| Right | **3 cm** | 2.5 cm | ❌ |
| Top | **3 cm** | 2.5 cm | ❌ |
| Bottom | **3 cm** | 2.5 cm | ❌ |

---

## Font

```latex
\usepackage{pslatex}   % Times New Roman equivalent
```

| Setting | Current | BINUS Guide | Status |
|---|---|---|---|
| Font family | pslatex (Times New Roman) | Times New Roman | ✅ |
| Body size | 12pt (from documentclass) | 12pt | ✅ |
| Foreign words | `\textit{}` / `\f{}` alias | Italic | ✅ |
| Chapter/section titles | `\bfseries` | Bold | ✅ |

---

## Line Spacing (`Awal_konfigurasi.tex`)

```latex
\usepackage{setspace}
  \onehalfspacing   % ← set first
  \doublespacing    % ← overrides the above
```

| Setting | Current | BINUS Guide | Status |
|---|---|---|---|
| Body line spacing | **2.0 (double)** — `\doublespacing` wins | 1.5 spasi | ❌ |

> `\doublespacing` is declared after `\onehalfspacing` and overrides it. The entire document body is currently double-spaced.

---

## Page Numbering (`Skripsi.tex`)

Three custom `fancyhdr` styles are defined and applied correctly:

| Style name | Applied to | Page number position | BINUS Guide | Status |
|---|---|---|---|---|
| `romanstyle` | Front matter (`\pagestyle{romanstyle}`) | Center bottom (`\fancyfoot[C]`) | Roman, center bottom | ✅ |
| `chapterstart` | Chapter opening page (via `\renewcommand{\chapter}`) | Center bottom (`\fancyfoot[C]`) | Center bottom | ✅ |
| `mainstyle` | Body chapters (`\pagestyle{mainstyle}`) | Odd → top-right (`RO`), Even → top-left (`LE`) | Odd top-right, Even top-left | ✅ |

> Cover page numbering is suppressed with `\pagenumbering{gobble}` before `\pagenumbering{roman}`. ✅  
> Roman numbering starts at `\setcounter{page}{2}` (cover = 1, no printed number). ✅

**Conflict warning**: `Awal_konfigurasi.tex` also calls `\pagestyle{fancy}` at the end with `\fancyfoot[C]{\thepage}`. This fires during package loading but is overridden later in `Skripsi.tex` by the explicit `\pagestyle{romanstyle}` / `\pagestyle{mainstyle}` calls — so the final output should be correct, but the redundant code in `Awal_konfigurasi.tex` is noise.

---

## Chapter / Section Numbering (`Awal_konfigurasi.tex`)

```latex
\renewcommand{\chaptername}{Chapter}
\renewcommand{\thechapter}{\Roman{chapter}}    % ← Roman: I, II, III...
\renewcommand{\thesection}{\arabic{chapter}.\arabic{section}}
```

| Setting | Current | BINUS Guide | Status |
|---|---|---|---|
| Chapter label | `Chapter` (English) + Roman numerals (I, II…) | `BAB` + Arabic (1, 2, 3…) | ❌ |
| Section numbering | `1.1`, `2.3` etc. (Arabic) | — | ✅ |

> The `.tex` chapter files (`bab1.tex` etc.) use `\chapter{\babSatu}` so the chapter *title* is Indonesian, but the prefix before it will render as "Chapter I", "Chapter II" via the fncychap style. Whether this is intentional or a mismatch depends on the supervisor's preference.

---

## Figure & Table Labels (`Awal_konfigurasi.tex`)

```latex
\renewcommand{\figurename}{\bo{Figure}}      % English, bold
\renewcommand{\thefigure}{\arabic{chapter}.\arabic{figure}}

\renewcommand{\tablename}{\bo{Table}}        % English, bold
\renewcommand{\thetable}{\arabic{chapter}.\arabic{table}}
```

| Element | Current | BINUS Guide | Status |
|---|---|---|---|
| Figure label | `Figure` (English, bold) | `Gambar` (Indonesian) | ❌ |
| Table label | `Table` (English, bold) | `Tabel` (Indonesian) | ❌ |
| Numbering format | `1.1`, `2.3` (chapter.sequence) | — | ✅ |
| Caption font size | `footnotesize` (via caption package) | 12pt | ❌ |

> Caption package: `\usepackage[font=footnotesize, ...]{caption}` — captions render smaller than 12pt.

---

## Bibliography (`Awal_konfigurasi.tex` + `Skripsi.tex`)

```latex
% Awal_konfigurasi.tex:
\renewcommand{\bibname}{REFERENCES}           % English

% Skripsi.tex (overrides):
\renewcommand\bibname{DAFTAR PUSTAKA}
\bibliographystyle{apacite}
\bibliography{ref}
```

| Setting | Current (effective) | BINUS Guide | Status |
|---|---|---|---|
| Bibliography heading | `DAFTAR PUSTAKA` (from `Skripsi.tex` override) | `REFERENSI` | ⚠️ close |
| Citation style | `apacite` (APA) | APA | ✅ |
| Citation package | `\usepackage[natbibapa]{apacite}` | — | ✅ |

> BINUS guide says `REFERENSI`; current output uses `DAFTAR PUSTAKA`. Minor label difference — confirm with supervisor.

---

## List of Figures / Tables Names

```latex
% Awal_konfigurasi.tex (initial):
\renewcommand{\listfigurename}{LIST OF FIGURES}
\renewcommand{\listtablename}{LIST OF TABLES}

% Skripsi.tex (overrides at TOC generation):
\renewcommand{\listfigurename}{Daftar Gambar}
\renewcommand{\listtablename}{Daftar Tabel}
```

Effective output: `Daftar Gambar` / `Daftar Tabel` ✅

---

## Chapter Style

```latex
\usepackage[ConnyRevised]{fncychap}
```

This applies a decorative "Conny Revised" style to chapter headings (large bold title with horizontal rules). The BINUS guide specifies plain centered title — this visual style may not comply strictly with the guide.

---

## Build Pipeline (`.latexmkrc`)

```perl
$out_dir = 'build';
$pdf_mode = 1;                   # pdflatex
$pdflatex = 'pdflatex -interaction=nonstopmode -synctex=1 %O %S';
$bibtex_use = 2;                 # run bibtex when needed
```

Output goes to `build/Skripsi.pdf`. GitHub Actions CI runs `latexmk` on push to `main` and commits the built PDF.

---

## Summary: Deviations from BINUS Guide

| # | Issue | Current | Should be | File to fix |
|---|---|---|---|---|
| 1 | **Right/Top/Bottom margin** | 3 cm | 2.5 cm | `Awal_konfigurasi.tex` line 82 |
| 2 | **Line spacing** | Double (2.0) | 1.5 | `Awal_konfigurasi.tex` lines 72–74 |
| 3 | **Chapter numbering** | Roman (I, II…) | Arabic (1, 2…) | `Awal_konfigurasi.tex` line 313 |
| 4 | **Figure label** | `Figure` | `Gambar` | `Awal_konfigurasi.tex` line 317 |
| 5 | **Table label** | `Table` | `Tabel` | `Awal_konfigurasi.tex` line 322 |
| 6 | **Caption font size** | `footnotesize` | 12pt (normal) | `Awal_konfigurasi.tex` line 87 |
| 7 | **Bibliography heading** | `DAFTAR PUSTAKA` | `REFERENSI` | `Skripsi.tex` line 185 |
| 8 | **Chapter heading style** | fncychap `ConnyRevised` (decorative) | Plain centered bold | `Awal_konfigurasi.tex` line 113 |
