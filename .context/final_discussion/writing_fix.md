---
name: writing-fix
description: Tracking file for thesis writing fixes — config discrepancies vs BINUS guide and unnecessary bold in paragraph body
metadata:
  type: project
---

# Writing Fix Tracker
Created: 2026-06-14

---

## A. LaTeX Config Discrepancies (vs BINUS Guide)

Source: [`thesis-config-audit.md`]
(thesis-config-audit.md)  
File to edit: `Awal_konfigurasi.tex` (unless noted)

| # | Issue | Current | Target | Status |
|---|---|---|---|---|
| A1 | Right/Top/Bottom margin | 3 cm | 2.5 cm | ⏳ pending |
| A2 | Line spacing | `\doublespacing` (2.0) | `\onehalfspacing` (1.5) | ⏳ pending |
| A3 | Chapter number style | `\Roman{chapter}` (I, II…) | `\arabic{chapter}` (1, 2…) | ⏳ pending |
| A4 | Figure label | `Figure` (English) | `Gambar` (Indonesian) | ⏳ pending |
| A5 | Table label | `Table` (English) | `Tabel` (Indonesian) | ⏳ pending |
| A6 | Caption font size | `footnotesize` | `normalsize` (12pt) | ⏳ pending |
| A7 | Bibliography heading | `DAFTAR PUSTAKA` | `REFERENSI` | ⏳ pending |
| A8 | Chapter heading style | fncychap `ConnyRevised` | plain centered bold | ⏳ pending |

---

## B. Unnecessary Bold in Paragraph Body

Rule: Bold in paragraph body is only valid for the **first-time introduction of a key term** (and only when truly unavoidable). Model names, numbers, and partial phrases being bolded are not standard academic practice.

**Legitimate bold (keep):**
- Table/figure column headers (`\textbf{Model}`, `\textbf{MRR}`, etc.)
- TikZ class/ERD diagram node names (`\textbf{Supervisor}`, etc.)
- Primary key markers (`\underline{\textbf{id}}`)
- List item labels with colon pattern: `\textbf{Tahap X …:}`, `\textbf{Fase 1 —…:}`, `\textbf{Inisialisasi:}`, metric definition labels (`\textbf{MRR}`, `\textbf{Hit@1}` etc. in itemize)
- Saran list item titles: `\textbf{Perluasan cakupan…}` (bab5)

### B1 — bab2.tex: Entire paragraphs wrapped in `\textbf{}` (lines 167, 169, 171)
Three consecutive paragraphs under `\subsection{Evaluasi Sistem Rekomendasi}` have the entire paragraph body wrapped in `\textbf{}`. This renders the full section opening in bold, which is wrong.
- **Status:** ✅ Fixed (2026-06-14)

### B2 — bab4.tex: Model names bold in paragraph text (lines 97, 121, 123, 125, 129, 157, 185)
Model names `BAAI/bge-m3`, `intfloat/multilingual-e5-large-instruct`, `Qwen/Qwen3-Embedding-0.6B` used bold throughout paragraph text. Model names in body text should be plain (or `\texttt{}` for code identifiers).
- **Status:** ✅ Fixed (2026-06-14)

### B3 — bab4.tex: Model names bold in table data cells (lines 83, 85, 87, 107, 109, 111)
First-column data cells in comparison tables had bold model names. Table data rows should not be bold; only headers.
- **Status:** ✅ Fixed (2026-06-14)

### B4 — bab4.tex: Numbers bold in paragraph (lines 64, 66)
`\textbf{18 eksperimen}` and `\textbf{168 dari 171 mahasiswa}` — numbers/counts in body text should not be bold.
- **Status:** ✅ Fixed (2026-06-14)

### B5 — bab4.tex: Key-phrase bold mid-paragraph (lines 127, 181, 217)
- `\textbf{sebuah sistem rekomendasi dinilai dari kualitas pemeringkatannya}` — entire conclusion sentence bolded
- `\textbf{tidak memberikan pengaruh nyata}` — finding phrase bolded
- `\textbf{identik}` — single word bolded
- **Status:** ✅ Fixed (2026-06-14)

### B6 — bab4.tex: Table delta values bold (lines 145, 149)
`\textbf{+5,8pp}` and `\textbf{-6,4pp}` in extra_docs table data cells.
- **Status:** ✅ Fixed (2026-06-14)

### B7 — bab3.tex: Single word `\textbf{satu}` in paragraph (line 187)
Over-emphasis on the word "satu" (one document) in supervisor representation paragraph.
- **Status:** ✅ Fixed (2026-06-14)

### B8 — bab3.tex: `\textbf{EPC}` acronym in paragraph body (lines 368, 981)
Acronym EPC bolded mid-paragraph in two places. Acronyms in body text should be plain.
- **Status:** ✅ Fixed (2026-06-14)

### B9 — bab5.tex: Model name bold in Simpulan (line 17)
`\textbf{BAAI/bge-m3}` in the simpulan list item should be plain text.
- **Status:** ✅ Fixed (2026-06-14)

---

## Config Fix Status (Section A — not yet applied)

All 8 config fixes (A1–A8) require edits to `Awal_konfigurasi.tex` and `Skripsi.tex`.  
These affect the visual layout of the entire compiled PDF and should be applied together in one pass to avoid repeated compilation.