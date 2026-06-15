---
name: writing-errors
description: Comprehensive map of writing errors across the full thesis — structural, typographic, grammatical, and stylistic. Created 2026-06-15.
metadata:
  type: project
---

# Thesis Writing Error Map
> Created: 2026-06-15  
> Scope: bab1.tex, bab2.tex, bab3.tex, bab4.tex, bab5.tex, pengantar.tex, abstrak.tex  
> Status legend: ❌ Open | ✅ Fixed

---

## Category S — Structural Errors (section/chapter structure)

### S1 — bab3.tex line 78: Empty `\subsection{Analisis}`
**Severity: KRITIS**  
`\subsection{Analisis}\label{analisis}` has zero body content — no paragraph follows before the next `\subsection`. Renders as an orphan TOC entry (§3.2.1) with no content.  
**Fix:** Delete the line (content is already covered by the two Analisis subsections below it).  
**Status:** ✅ Fixed 2026-06-15

---

### S2 — bab3.tex line 90: Duplicate `\subsection{Analisis Permasalahan}`
**Severity: KRITIS**  
Two consecutive subsections share the identical title "Analisis Permasalahan":
- Line 80: content = manual EPC process problems ✅ correct title
- Line 90: content = EPC actor role + student/supervisor as data entities ← title is WRONG

The second one discusses who the actors are, not what the problems are. TOC shows both as §3.2.2 and §3.2.3 with duplicate labels.  
**Fix:** Rename line 90 to `\subsection{Analisis Aktor Sistem}` and update `\label{analisis-aktor-sistem}`.  
**Status:** ✅ Fixed 2026-06-15

---

## Category T — Typos in Section/Subsection Titles

### T1 — bab1.tex line 106: `\subsection{Praposes Data}` 
**Severity: KRITIS** (appears in TOC and Daftar Isi)  
"Praposes" is a misspelling. The body text on line 108 correctly writes "praproses data". The TOC also shows "Praposes Data" (p. 6).  
**Fix:** `\subsection{Praproses Data}` — update label accordingly.  
**Status:** ✅ Fixed 2026-06-15

---

### T2 — bab2.tex line 305: `\subsection{Post Forwarding}`
**Severity: KRITIS** (appears in TOC at §2.1.28)  
"Post" is a misspelling of "Port". Body text line 307 correctly writes "Port forwarding merupakan teknik jaringan…".  
**Fix:** `\subsection{Port Forwarding}` — update label.  
**Status:** ✅ Fixed 2026-06-15

---

## Category G — Grammatical Errors in Body Text

### G1 — bab1.tex line 11: Capital letter after comma
**Severity: SEDANG**  
"Namun, **B**erdasarkan hasil observasi…" — "Berdasarkan" should not be capitalized mid-sentence after a comma.  
**Fix:** `Namun, berdasarkan hasil observasi…`  
**Status:** ✅ Fixed 2026-06-15

---

### G2 — bab1.tex line 130: Duplicate word "mengenai"
**Severity: SEDANG**  
Sistematika Penulisan BAB 1 description: "membahas mengenai **mengenai** latar belakang…" — "mengenai" appears twice consecutively.  
**Fix:** Remove one: "membahas mengenai latar belakang…"  
**Status:** ✅ Fixed 2026-06-15

---

### G3 — bab1.tex lines 128–146: "Pada bab ini membahas…" (5 occurrences)
**Severity: SEDANG**  
All five chapter descriptions in Sistematika Penulisan use the same grammatically incorrect pattern:

| Line | Current | Problem |
|------|---------|---------|
| 130 | "Pada bab ini membahas mengenai…" | Subject-verb mismatch: "Pada bab ini" is adverbial, cannot be subject of "membahas" |
| 134 | "Pada bab ini berfokus pada…" | Same issue |
| 138 | "Pada bab ini memberikan penjelasan…" | Same issue |
| 142 | "Pada bab ini membahas proses…" | Same issue + missing period at end |
| 146 | "Pada bab ini membahas mengenai…" | Same issue |

**Fix pattern:** Either change to "Bab ini membahas…" (active) or "Pada bab ini dibahas…" (passive). Also add missing period after line 142.  
**Status:** ✅ Fixed 2026-06-15

---

### G4 — bab1.tex line 142: Missing period at end of BAB 4 description
**Severity: MINOR** (part of G3 but noted separately)  
"…serta analisis dan pembahasan hasil evaluasi yang diperoleh" — sentence ends without a period.  
**Fix:** Add `.` at end.  
**Status:** ✅ Fixed 2026-06-15

---

### G5 — bab2.tex lines 193–194: Duplicate opening sentences in Recall@K
**Severity: SEDANG**  
```
Recall@K merupakan metrik evaluasi yang mengukur proporsi item relevan yang berhasil muncul
dalam K rekomendasi teratas. Recall@K mengukur proporsi item relevan yang berhasil
direkomendasikan oleh sistem dalam K rekomendasi teratas.
```
The second sentence is a near-verbatim restatement of the first — both say the same thing. Reads as copy-paste artifact.  
**Fix:** Delete the second sentence or replace with substantive content (e.g., mention context of use or formula introduction).  
**Status:** ✅ Fixed 2026-06-15

---

### G6 — bab2.tex lines 211–213: "Mean Reciprocal Rank (MRR)" repeated in full twice
**Severity: SEDANG**  
```
Mean Reciprocal Rank (MRR) merupakan metrik evaluasi yang mengukur posisi item relevan pertama
dalam daftar rekomendasi. Mean Reciprocal Rank (MRR) mengukur seberapa cepat item relevan
pertama muncul dalam daftar rekomendasi.
```
Full acronym expansion "Mean Reciprocal Rank (MRR)" appears in both sentences. Second instance should use just "MRR". Same copy-paste artifact pattern.  
**Fix:** "MRR mengukur seberapa cepat…" on second sentence.  
**Status:** ✅ Fixed 2026-06-15

---

### G7 — bab2.tex lines 233–235: Duplicate opening sentences in Cosine Similarity Score
**Severity: SEDANG**  
```
Cosine similarity score merupakan ukuran kemiripan antara dua vektor berdasarkan sudut
di antaranya. Cosine similarity mengukur kemiripan antara dua vektor embedding berdasarkan
sudut di antara keduanya.
```
Same pattern as G5 and G6 — two consecutive sentences say the same thing in nearly identical wording.  
**Fix:** Delete or replace the second sentence with content about score range or usage context.  
**Status:** ✅ Fixed 2026-06-15

---

## Category F — LaTeX Formatting Errors

### F1 — bab1.tex line 102: Tilde `~` inside section title
**Severity: MINOR**  
`\subsection{Pengumpulan~Data}` — the non-breaking space `~` inside a section title is unnecessary and may produce an extra visible space or compile warning. Section titles don't need `~` for line-break prevention.  
**Fix:** `\subsection{Pengumpulan Data}`  
**Status:** ✅ Fixed 2026-06-15

---

### F2 — bab2.tex line 165: `\textbf{}` wrapping inside `\texorpdfstring` on subsection title
**Severity: MINOR**  
```latex
\subsection{\texorpdfstring{\textbf{Evaluasi Sistem Rekomendasi}}{Evaluasi Sistem Rekomendasi}}
```
This is the only subsection in BAB 2 with `\textbf{}` inside the title. All other subsections use plain text. Subsection titles are already bold via the document class — this double-bold is inconsistent and unnecessary.  
**Fix:** `\subsection{Evaluasi Sistem Rekomendasi}` (remove `\texorpdfstring` and `\textbf`).  
**Status:** ✅ Fixed 2026-06-15

---

### F3 — bab4.tex line 45: Semicolon-space separator in table cell
**Severity: MINOR**  
Table row: `ORM ; Database` — uses semicolon-with-spaces as separator. Should use a standard separator.  
**Fix:** `ORM / Database` or `ORM \& Database`  
**Status:** ✅ Fixed 2026-06-15

---

### F4 — bab4.tex lines 13, 35: 4-column tabular with 2-column data
**Severity: MINOR**  
Both hardware and software specification tables define a 4-column `tabular` spec (`|p{2cm}|p{5cm}|p{2cm}|p{3cm}|`) but every row only has 2 cells (`Komponen & Spesifikasi`). The extra 2 column specs are unused — produces LaTeX underfull hbox warnings and wastes whitespace.  
**Fix:** Reduce column spec to `|p{4cm}|p{9cm}|` (or similar 2-column layout).  
**Status:** ✅ Fixed 2026-06-15

---

### F5 — pengantar.tex line 17: Missing space before "selaku"
**Severity: MINOR**  
`Dr. Boby Siswanto, S.T., M.T.selaku Head of Department…` — no space between the degree suffix and "selaku".  
**Fix:** `Dr. Boby Siswanto, S.T., M.T. selaku Head of Department…`  
**Status:** ✅ Fixed 2026-06-15

---

## Summary Table

| # | File | Line(s) | Category | Severity | Status |
|---|------|---------|----------|----------|--------|
| S1 | bab3.tex | 78 | Structural — empty section | KRITIS | ✅ |
| S2 | bab3.tex | 90 | Structural — duplicate title | KRITIS | ✅ |
| T1 | bab1.tex | 106 | Typo in section title | KRITIS | ✅ |
| T2 | bab2.tex | 305 | Typo in section title | KRITIS | ✅ |
| G1 | bab1.tex | 11 | Capital after comma | SEDANG | ✅ |
| G2 | bab1.tex | 130 | Duplicate word "mengenai" | SEDANG | ✅ |
| G3 | bab1.tex | 128–146 | "Pada bab ini membahas" × 5 | SEDANG | ✅ |
| G4 | bab1.tex | 142 | Missing period | MINOR | ✅ |
| G5 | bab2.tex | 193–194 | Duplicate sentences Recall@K | SEDANG | ✅ |
| G6 | bab2.tex | 211–213 | Full acronym repeated in MRR | SEDANG | ✅ |
| G7 | bab2.tex | 233–235 | Duplicate sentences CosSim | SEDANG | ✅ |
| F1 | bab1.tex | 102 | Tilde `~` in section title | MINOR | ✅ |
| F2 | bab2.tex | 165 | `\textbf` in subsection title | MINOR | ✅ |
| F3 | bab4.tex | 45 | Semicolon separator in table | MINOR | ✅ |
| F4 | bab4.tex | 13, 35 | 4-col spec / 2-col data | MINOR | ✅ |
| F5 | pengantar.tex | 17 | Missing space before "selaku" | MINOR | ✅ |

**Total: 16 errors** — 4 KRITIS, 6 SEDANG, 6 MINOR  
**KRITIS errors (S1, S2, T1, T2) should be fixed before submission** — they appear in the TOC.
