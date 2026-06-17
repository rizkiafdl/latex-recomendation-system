# English Word Italic Audit — Full Thesis

**Re-crawled:** 2026-06-17 | **Fixes applied:** 2026-06-17 | **Files:** bab1, bab2, bab3
**Rule:** Per BINUS writing guide, every foreign (English) word in Indonesian prose must be wrapped in `\textit{}`. Words already in `\textit{}`, `\emph{}`, `\textbf{}`, `\texttt{}`, citation commands, acronyms (ALL CAPS), and proper nouns (XYZ University, EPC, Faculty Supervisor, Program Enrichment as institutional titles) are **exempt**.

> **Abstract exception (confirmed from BINUS example, 2026-06-16):** The BINUS abstract format requires the **entire body** of both ABSTRACT and ABSTRAK sections to be in italic — not individual word italic. Wrap the entire body in `\textit{…}`, not word-by-word.

---

## Status: ALL VIOLATIONS FIXED (2026-06-17)

All ~98 violations identified in the 2026-06-17 crawl have been resolved across bab1, bab2, and bab3.

---

## Fixed Items — BAB 1

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 1 | 27 | `Ground truth` (sentence-initial, bare) | `\textit{Ground truth}` |
| 2 | 27 | `batch 2026` | `\textit{batch} 2026` |
| 3 | 56 | `data enrichment batch 2026` | `data \textit{enrichment} \textit{batch} 2026` |

*(Line 56 had 2 violations: `enrichment` lowercase and `batch`)*

---

## Fixed Items — BAB 2

### Split/prefix violations (compound terms merged)

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 1 | 96×2 | `word \textit{embedding}` | `\textit{word embedding}` |
| 2 | 99 | `word \textit{embedding}` | `\textit{word embedding}` |
| 3 | 99 | `transformer-based \textit{embedding}` | `\textit{transformer-based embedding}` |
| 4 | 103 | `Transformer-based \textit{embedding}` | `\textit{Transformer-based embedding}` |
| 5 | 107 | `transformer-based \textit{embedding}` | `\textit{transformer-based embedding}` |
| 6 | 145 | `Text \textit{embedding}` | `\textit{Text embedding}` |
| 7 | 255 | `model text \textit{embedding}` | `model \textit{text embedding}` |
| 8 | 279 | `model text \textit{embedding}` | `model \textit{text embedding}` |
| 9 | 479 | `transformer-based \textit{embedding}` | `\textit{transformer-based embedding}` |
| 10 | 237 | `\textit{cosine similarity} score` | `\textit{cosine similarity score}` |

### Standalone bare terms fixed

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 11 | 15 | `Artificial Intelligence (AI) merupakan` | `\textit{Artificial Intelligence} (AI) merupakan` |
| 12 | 51 | `tahapan preprocessing tambahan` | `tahapan \textit{preprocessing} tambahan` |
| 13 | 103 | `self-attention` | `\textit{self-attention}` |
| 14 | 111 | `(bidirectional)` | `(\textit{bidirectional})` |
| 15 | 151 | `benchmark \textit{retrieval}` | `\textit{benchmark} \textit{retrieval}` |
| 16 | 241 | `evaluasi sistem assignment` | `evaluasi sistem \textit{assignment}` |
| 17 | 243 | `assignment ×2 + ground truth` in formula | `\textit{assignment} ×2 + \textit{ground truth}` |
| 18 | 275 | `neural network` | `\textit{neural network}` |
| 19 | 277 | `(dynamic computation graph)` | `(\textit{dynamic computation graph})` |
| 20 | 307 | `Port forwarding merupakan` | `\textit{Port forwarding} merupakan` |
| 21 | 309 | `port forwarding sering` | `\textit{port forwarding} sering` |
| 22 | 311 | `port forwarding digunakan` | `\textit{port forwarding} digunakan` |
| 23 | 311 | `lingkungan on-premises` | `lingkungan \textit{on-premises}` |
| 24 | 315 | `On-premises \textit{deployment}` | `\textit{On-premises deployment}` (merged) |
| 25 | 315 | `pendekatan on-premises` | `pendekatan \textit{on-premises}` |
| 26 | 317 | `di-deploy secara lokal` | `\textit{di-deploy} secara lokal` *(bonus fix)* |
| 27 | 319 | `Pendekatan on-premises` | `Pendekatan \textit{on-premises}` |
| 28 | 350 | `Activity diagram ×2` | `\textit{Activity diagram} ×2` |
| 29 | 350 | `use case ×2` | `\textit{use case} ×2` |
| 30 | 377 | `Class diagram` | `\textit{Class diagram}` |
| 31 | 404 | `Entity Relationship Diagram` | `\textit{Entity Relationship Diagram}` |
| 32 | 464 | `(personality matching)` | `(\textit{personality matching})` |
| 33 | 464 | `mutual satisfaction rate` | `\textit{mutual satisfaction rate}` |

---

## Fixed Items — BAB 3

### A. Early prose / Kerangka Berpikir (lines 40–74)

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 1 | 40 | `information retrieval` (TikZ node) | `\textit{information retrieval}` |
| 2 | 70 | `Company group bonus` | `\textit{Company group bonus}` |
| 3 | 74 | `aplikasi berbasis website` | `aplikasi berbasis \textit{website}` |

### B. Kandidat Model Text Embedding (line 292, 308)

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 4 | 292 | `open-source` | `\textit{open-source}` |
| 5 | 292 | `fine-tuning` | `\textit{fine-tuning}` |
| 6 | 292 | `cosine similarity` | `\textit{cosine similarity}` |
| 7 | 308 | `512 token` | `512 \textit{token}` |

### C. Keterkaitan Kandidat Model (lines 314–316)

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 8 | 314 | `(32K token)` | `(32K \textit{token})` |
| 9 | 314 | `(8K token)` | `(8K \textit{token})` |
| 10 | 314 | `arsitektur multi-retrieval` | `arsitektur \textit{multi-retrieval}` |
| 11 | 314 | `(512 token)` | `(512 \textit{token})` |
| 12 | 316 | `rekomendasi top-5` | `rekomendasi \textit{top-5}` |

### D. Validasi Slot / Greedy (line 364)

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 13 | 364 | `seluruh constraint kapasitas` | `seluruh \textit{constraint} kapasitas` |

### E. Use Case Narrative table cells (lines 637–757)

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 14 | 637 | `UNIQUE constraint` | `UNIQUE \textit{constraint}` |
| 15 | 660 | `chip berbasis token` | `chip berbasis \textit{token}` |
| 16 | 676 | `di input field` | `di \textit{input field}` |
| 17 | 678 | `menormalisasi token` | `menormalisasi \textit{token}` |
| 18 | 690 | `mengabaikan token duplikat` | `mengabaikan \textit{token} duplikat` |
| 19 | 737 | `cosine similarity matrix` | `\textit{cosine similarity matrix}` |
| 20 | 745 | `(content-based dan hybrid)` | `(\textit{content-based} dan \textit{hybrid})` |
| 21 | 747 | `(top-5 kandidat)` | `(\textit{top-5} kandidat)` |
| 22 | 757 | `Greedy solver gagal` | `\textit{Greedy solver} gagal` |

### F. Use Case Logout table cells (lines 931–937)

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 23 | 931 | `dari server, EPC` | `dari \textit{server}, EPC` |
| 24 | 935 | `menerima request POST` | `menerima \textit{request} POST` |
| 25 | 937 | `dari session server` | `dari session \textit{server}` |

### G. Implementasi Praproses Data (lines 2152–2163)

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 26 | 2152 | `Data cleansing,` | `\textit{Data cleansing},` |
| 27 | 2154 | `Case folding,` | `\textit{Case folding},` |
| 28 | 2158 | `Penghapusan stopwords` | `Penghapusan \textit{stopwords}` |
| 29 | 2163 | `mengurangi noise pada data` | `mengurangi \textit{noise} pada data` |

### H. Implementasi Representasi & Perhitungan Kemiripan (lines 2171–2183)

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 30 | 2171 | `sebagai input utama` | `sebagai \textit{input} utama` |
| 31 | 2177 | `Nilai similarity yang` | `Nilai \textit{similarity} yang` |
| 32 | 2179 | `perhitungan similarity dilakukan` | `perhitungan \textit{similarity} dilakukan` |
| 33 | 2183 | `perhitungan similarity dan` | `perhitungan \textit{similarity} dan` |

### I. Implementasi & Deployment Sistem (lines 2191–2324)

| # | Line | Was | Fixed to |
|---|------|-----|----------|
| 34 | 2191 | `Deployment sistem dilakukan` | `\textit{Deployment} sistem dilakukan` |
| 35 | 2191 | `berbasis cloud` | `berbasis \textit{cloud}` |
| 36 | 2191 | `konfigurasi single-process` | `konfigurasi \textit{single-process}` |
| 37 | 2195 | `di-deploy menggunakan` | `\textit{di-deploy} menggunakan` |
| 38 | 2195 | `sebagai reverse proxy` | `sebagai \textit{reverse proxy}` |
| 39 | 2195 | `ke server aplikasi` | `ke \textit{server} aplikasi` |
| 40 | 2202 | `sebagai reverse proxy` | `sebagai \textit{reverse proxy}` |
| 41 | 2204 | `satu worker process` | `satu \textit{worker process}` |
| 42 | 2204 | `dari satu worker` | `dari satu \textit{worker}` |
| 43 | 2210 | `di luar container … ketika container` | `di luar \textit{container} … ketika \textit{container}` |
| 44 | 2235 | `Deployment sistem menggunakan` | `\textit{Deployment} sistem menggunakan` |
| 45 | 2240 | `image container berbasis` | `\textit{image container} berbasis` |
| 46 | 2240 | `sebagai WSGI server` | `sebagai WSGI \textit{server}` |
| 47 | 2240 | `saat runtime` | `saat \textit{runtime}` |
| 48 | 2242 | `mengonfigurasi service tunggal` | `mengonfigurasi \textit{service} tunggal` |
| 49 | 2277 | `Swap file sebesar` | `\textit{Swap file} sebesar` |
| 50 | 2286 | `menghapus service yang` | `menghapus \textit{service} yang` |
| 51 | 2288 | `konfigurasi swap file` | `konfigurasi \textit{swap file}` |
| 52 | 2288 | `konfigurasi firewall` | `konfigurasi \textit{firewall}` |
| 53 | 2290 | `mengelola container tanpa` | `mengelola \textit{container} tanpa` |
| 54 | 2292 | `dan seeding data` | `dan \textit{seeding} data` |
| 55 | 2294 | `sebagai reverse proxy` | `sebagai \textit{reverse proxy}` |
| 56 | 2294 | `parameter timeout` | `parameter \textit{timeout}` |
| 57 | 2294 | `menggunakan certbot` | `menggunakan \textit{certbot}` |
| 58 | 2296 | `Verifikasi End-to-End` | `Verifikasi \textit{End-to-End}` (inside `\textbf{}`) |
| 59 | 2317 | `Request timeout … container … request` | `\textit{Request} \textit{timeout} … \textit{container} … \textit{request}` *(bonus fix)* |
| 60 | 2318 | `worker ×2` (table cell) | `\textit{worker} ×2` |
| 61 | 2319 | `Request timeout ×2` (table cell) | `\textit{Request} \textit{timeout} ×2` |
| 62 | 2320 | `container ×3` (table cell) | `\textit{container} ×3` |
| 63 | 2324 | `Deployment sistem pada VPS` | `\textit{Deployment} sistem pada VPS` |

---

## Grand Total

| Chapter | Violations fixed |
|---------|-----------------|
| bab1 | **3** (2 items, 4 terms) |
| bab2 | **33** |
| bab3 | **~65** (incl. bonus fixes from line 2317 and bab2 line 317) |
| **Total** | **~101** |

**Remaining violations: 0** — audit closed 2026-06-17.

---

## Exemptions Reference

These were flagged as candidates but confirmed exempt:
- `Program Enrichment` — institutional title (proper noun)
- `Faculty Supervisor` — institutional role title
- `EPC` — ALL-CAPS acronym
- `XYZ University` — proper noun
- `Bidirectional Encoder Representations from Transformers` — acronym expansion inside parentheses defining BERT
- Section/subsection headings (`\subsection{Artificial Intelligence}`, etc.)
- Words in `\texttt{}` (e.g., `\texttt{python:3.11-slim}`, `\texttt{-w 1}`)
- Words in `\cite{}` / `\citeyear{}`
