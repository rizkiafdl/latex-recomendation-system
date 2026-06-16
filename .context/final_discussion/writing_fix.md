---
name: writing-fix
description: Tracking file for thesis writing fixes — config discrepancies vs BINUS guide and unnecessary bold in paragraph body
metadata:
  type: project
---

# Writing Fix Tracker
Created: 2026-06-14

---

## AGENT GUIDE RAIL — Italic English Word Fixer

> **Purpose:** This section tells an agent exactly how to apply the remaining bare-English-word italic fixes in bab1–5. Read this fully before touching any `.tex` file.

### Task
Wrap bare English loanwords/technical terms in `\textit{}` in the thesis body text, following the BINUS writing guide rule: *all English words used within Indonesian prose must be italicized*.

### The Four Rules

1. **Only wrap bare words in prose.** If a word is already inside `\textit{}`, `\textbf{\textit{}}`, `{\itshape …}`, or `\emph{}` — skip it. Never double-wrap.
2. **Never touch non-prose contexts.** Do not italicize words inside:
   - `\label{}`, `\ref{}`, `\cite{}`, `\caption{}` arguments
   - TikZ/diagram node names, `\textbf{}` column headers in tables
   - Code blocks (`\texttt{}`, `lstlisting`, `verbatim`)
   - Section/subsection titles (`\section{}`, `\subsection{}`, etc.)
   - Comments (`% ...`)
3. **Replace-all is safe only when the term never appears in a non-prose context in that file.** When in doubt, use targeted replacement with enough surrounding context to ensure uniqueness.
4. **After editing a file, do a sanity grep** to confirm no `\label{...\textit`, `\cite{...\textit`, or `\ref{...\textit` was introduced. These are hard LaTeX errors (see history: this exact bug broke the build on 2026-06-16).

### Replacement Pattern
```
bare:    word
wrapped: \textit{word}

bare:    multi word phrase
wrapped: \textit{multi word phrase}

bare:    word-with-hyphen
wrapped: \textit{word-with-hyphen}

special: word-nya  (Indonesian suffix attached)
wrapped: \textit{word}-nya
```

### How to Read the Fix Tables (C1–C5)
- **replace_all**: use Python `content.replace('term', r'\textit{term}')` — safe because term does not appear in labels/refs in that file.
- **targeted (line N)**: use surrounding sentence context as the old_string to ensure uniqueness. Read the line first.
- **Word fix (not italic)**: rename the Indonesian typo/wrong word — do not add `\textit`.

### Execution Order
1. Apply all **replace_all** entries for a file first (they're bulk, fast, no conflicts).
2. Then apply **targeted** entries one by one, reading the line before each edit.
3. Run `latexmk -pdf -output-directory=build Skripsi.tex` after completing each file.
4. Check the build log for any `\textit` inside `\label`/`\ref`/`\cite` error — fix immediately.

### Still-Pending Terms (P2–P3, lower priority — do after all C1–C5 are done)
See the **"Still Pending"** section at the bottom of this file. These are lower-priority terms; tackle P2 before P3.

---

## A. LaTeX Config Discrepancies (vs BINUS Guide)

Source: [`thesis-config-audit.md`]
(thesis-config-audit.md)  
File to edit: `Awal_konfigurasi.tex` (unless noted)

| # | Issue | Current | Target | Status |
|---|---|---|---|---|
| A1 | Right/Top/Bottom margin | 3 cm | 2.5 cm | ✅ Fixed (2026-06-14) |
| A2 | Line spacing | `\doublespacing` (2.0) | `\onehalfspacing` (1.5) | ✅ Fixed (2026-06-14) |
| A3 | Chapter number style | `\Roman{chapter}` (I, II…) | `\arabic{chapter}` (1, 2…) | ✅ Fixed (2026-06-14) |
| A4 | Figure label | `Figure` (English) | `Gambar` (Indonesian) | ✅ Fixed (2026-06-14) |
| A5 | Table label | `Table` (English) | `Tabel` (Indonesian) | ✅ Fixed (2026-06-14) |
| A6 | Caption font size | `footnotesize` | `normalsize` (12pt) | ✅ Fixed (2026-06-14) |
| A7 | Bibliography heading | `DAFTAR PUSTAKA` | `REFERENSI` | ✅ Fixed (2026-06-14) |
| A8 | Chapter heading style | fncychap `ConnyRevised` | plain centered bold | ✅ Fixed (2026-06-14) |

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

## Config Fix Status (Section A — all applied 2026-06-14)

All 8 config fixes applied to `ta.sty` (A1–A6, A8) and `Skripsi.tex` (A7).  
For A8: fncychap commented out, replaced with `titlesec` plain centered bold chapter format.

---

## C. English Word Italic Fixes (per `english_word_mapping.md`)

**Applied: 2026-06-16** | Source audit: `.context/final_discussion/english_word_mapping.md`

### C0 — abstrak.tex: Full format fix
- ✅ Line 29: `\textbf{\textit{ABSTRACT}}` → `\textbf{ABSTRACT}` (label bold-only)
- ✅ Lines 32–36: ABSTRACT body wrapped in `{\itshape …}` (full-body italic)
- ✅ Lines 44–48: ABSTRAK body wrapped in `{\itshape …}` (full-body italic)

### C1 — bab1.tex: All P1+P2 terms (replace_all where safe)
| Term | Occurrences | Method |
|------|-------------|--------|
| `semantic similarity` | 7 | replace_all |
| `ground truth` | 6 | replace_all |
| `decision support system` | 2 | replace_all |
| `backend` | 2 | replace_all |
| `batch` | 3 | replace_all |
| `machine learning` | 1 | replace_all |
| `cosine similarity` | 1 | replace_all |
| `embedding` (model embedding) | 1 | targeted |
| `spreadsheet` | 1 | targeted |
| `enrichment-nya` → `\textit{enrichment}-nya` | 1 | targeted |
| `stop words` | 1 | targeted |
| `natural language processing` | 1 | targeted |
| `input` | 1 | targeted |

### C2 — bab2.tex: P1+P2 terms
| Term | Method | Notes |
|------|--------|-------|
| `embedding` (30+) | replace_all | No pre-wrapped; all bare |
| `semantic similarity` | replace_all | |
| `cosine similarity` | replace_all | |
| `machine learning` (lowercase) | replace_all | |
| `deep learning` (lowercase) | replace_all | |
| `backend` | replace_all | |
| `deployment` | replace_all | |
| `pipeline` | replace_all | |
| `noise` | replace_all | |
| `stemming` | replace_all | |
| `stop word removal` | replace_all | |
| `regular expression` | replace_all | |
| `subword` | replace_all | |
| `decision support system` | targeted (line 9) | Line 379 already wrapped |
| `ground truth` | targeted (lines 175, 241) | Line 171 already wrapped |
| `Machine Learning` (sentence-start) | targeted (line 21) | |
| `Machine learning` (mid-sentence) | targeted (line 23) | |
| `Deep Learning` (sentence-start) | targeted (line 27) | |
| `deep neural networks` | targeted (line 27) | |
| `Text mining` (sentence-start) | targeted (line 39) | |
| `text mining` (mid-sentence) | targeted (lines 39, 57) | |
| `Text preprocessing` (sentence-start) | targeted (line 43) | |
| `text preprocessing` (mid-sentence) | targeted (line 45) | |
| `input` | targeted (line 43) | |
| `Word embedding` (sentence-start) | targeted (line 93) | |
| `information retrieval` | targeted (line 57) | |
| `Natural Language Processing` (sentence-start) | targeted (line 33) | |
| `baseline` | targeted (line 69) | |
| `Case Folding`, `Collapse Whitespace` | targeted (line 45) | |

### C3 — bab3.tex: P1+P2 terms
| Term | Method | Notes |
|------|--------|-------|
| `semantic similarity` | replace_all | |
| `decision support system` | replace_all | |
| `use case` | replace_all | |
| `deployment` | replace_all | (8+ occurrences in §2108–2297) |
| `backend` | replace_all | (line 2162) |
| `information retrieval` | targeted (line 66) | |
| `scoring`+`text embedding`+`company group bonus` | targeted (line 68) | |
| `hybrid` | targeted (line 72) | |
| `greedy capacity-constrained solver`+`constraint` | targeted (line 72) | |
| `fine-tuning` | targeted (line 169) | |
| `training`+`text embedding`+`open-source` | targeted (line 175) | |
| `waterfall` | targeted (line 2106) | |
| `batch` | targeted (line 331) | |
| `greedy solver` | targeted (lines 687, 717, 1562) | Line 358 already wrapped |
| `scoring`+`greedy` | targeted (line 1964) | |
| `embedding`+`scoring`+`greedy solver` | targeted (line 1562) | |
| Pipeline terms in UC table | targeted (line 687) | embedding, similarity, group bonus, etc. |

### C4 — bab4.tex: P1+P2 terms
| Term | Method | Notes |
|------|--------|-------|
| `meranking` → `memeringkat` | targeted (line 122) | Word fix, not italic |
| `semantic similarity` | targeted (line 10) | |
| `batch` | targeted (line 180) | |
| `database` | replace_all | 4 occurrences |
| `server-side` | targeted (line 615) | Line 381 already wrapped |
| `slot` | targeted (line 274) | |
| `mapping` (parenthetical) | targeted (line 278) | |
| `dataset` | targeted (line 325) | |

### C5 — bab5.tex: P1 term
| Term | Method |
|------|--------|
| `input` (line 35) | targeted |

---

### Still Pending (P2–P3 lower priority)

✅ **All P2–P3 items resolved (2026-06-16).** See C2b/C3b/C4b below.

---

### C2b — bab2.tex: P2–P3 bulk + targeted expansion (2026-06-16)

**Bulk replace_all (safe — no pre-wrap in file):**
`micro web framework`, `server-side rendering`, `session management`, `instruction tuning`, `siamese network`, `template engine`, `cross-encoding`, `text matching`, `semantic search`, `Hybrid Score`, `Content-Based`, `Assignment Match Rate`, `vector store`, `dot product`, `web server`, `client-server`, `client-side`, `single-process`, `single-user`, `request-response`, `trade-off`, `on-the-fly`, `file-based`, `de facto`, `software engineering`, `top-K`, `frontend`, `embedded`, `overhead`, `monolingual`, `firewall`, `browser`, `robust`, `entities`, `behavior`, `state`, `container`, `cloud`, `routing`, `templating`, `default`, `import`, `batch` (line 303 context), `level`, `query`

**Targeted edits (19 total):**
- `Semantic similarity merupakan ukuran` (sentence-start capital)
- `Cosine similarity merupakan metode` (sentence-start capital)
- `Cosine similarity score merupakan ukuran` (sentence-start capital)
- `Assignment match rate merupakan metrik` (body — capital-A form)
- `Embedding memungkinkan perbandingan teks` (sentence-start capital)
- `setiap token dipahami` and `8.192 token.` (bare `token` — skipped replace_all; `\label{...}` contains no token, but safer targeted)
- `proses encoding teks` and `Proses encoding dilakukan` (bare `encoding` — skipped replace_all: `cross-encoding` contains it as substring)
- `dan retrieval dibandingkan`, `benchmark retrieval lintas bahasa`, `pada tugas retrieval dan` (bare standalone `retrieval`)
- `dense retrieval, sparse retrieval, dan multi-vector retrieval` (three-phrase wrap)
- `dibandingkan model tanpa instruction.`
- `Setiap entity memiliki atribut` (bare `entity` — skipped replace_all: `\label{entity-relationship-diagram}` contains it)
- `sentence embedding untuk` → `\textit{sentence embedding}`
- `(mapping dosen pembimbing aktual` and `(mapping dosen aktual)` (parenthetical mapping)
- `dan Assignment Match.`

### C3b — bab3.tex: embedding + token + retrieval (2026-06-16)

**Placeholder-protected Python script** (protected: `\texttt{}`, TikZ `\node` lines, `embedding\_` attribute names, `\textbf{}` list labels, `\label{}`/`\cite{}`/`\ref{}` args, section titles, `\caption{}` args, pre-existing `\textit{embedding}` wraps at lines 181/242/297/356):
- ~35 bare `embedding` in body → `\textit{embedding}` (total 35 `\textit{embedding}` instances)
- 7 bare `text embedding` phrases → `\textit{text embedding}`
- 12 bare `Embedding` (capital) → `\textit{Embedding}`
- Fixed double-wrap at line 285: `\textit{\textit{Embedding}s…}` → `\textit{Embeddings…}` (suffix 's' escaped the protection)

**Targeted (3 edits):**
- Line 273: `dan retrieval.` → `dan \textit{retrieval}.`
- Line 273: `32K token,` → `32K \textit{token},`
- Line 279: `8.192 token,` → `8.192 \textit{token},`

### C4b — bab4.tex: P3 UI/table terms (2026-06-16)

**Multi-word replace_all:** `pipeline trigger` (2×), `error message` (2×), `semantic keywords` (1×), `searchable dropdown` (1×), `rules boost` (1×), `pop-up` (1×), `Extract, Transform, Load` (1×)

**Capital single-word targeted:** `Database` (2×), `Username` (6×), `Password` (2×), `Upload` (4×), `Toggle` (4×), `Keyword` (6×)

**Lowercase single-word replace_all:** `username` (6×), `password` (9×), `upload` (1×), `import` (5×), `export` (2×), `upsert` (bare at line 518 — line 607 was pre-wrapped), `toggle` (bare line 634 — line 63 pre-wrapped), `button` (1×), `dropdown` (2×), `keyword` (8×), `chip` (4×), `token` (1×), `tooltip` (1×), `metadata` (1×), `message` (4×)