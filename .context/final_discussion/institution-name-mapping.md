# Institution Name Mapping — Skripsi BINUS
*Last crawled: 2026-06-22 (second pass — complete)*

---

## Overview

The thesis uses two parallel naming schemes for the same institution:

| Zone | Name | Files |
|---|---|---|
| Front-matter & legal | **Universitas Bina Nusantara** / **BINUS University** | sampul, abstrak header, judul_dalam, orisinalitas, pernyataan, pengantar, lampiran |
| Body chapters | **Universitas XYZ** / **XYZ University** (pseudonym) | bab1, bab3, bab5, abstrak body, Awal_konfigurasi |

The user wants to replace ALL BINUS-family terms with XYZ equivalents. This document maps every occurrence and assigns a replacement strategy.

---

## Replacement Strategy Legend

| Tag | Meaning |
|---|---|
| `REPLACE` | Safe direct text replacement |
| `DECIDE` | Needs user decision — structural, functional, or ambiguous |
| `SKIP` | Cannot/should not change (real URL, real domain) |
| `DONE` | Already uses XYZ pseudonym |

---

## File-by-File Occurrence Map

### `Awal_konfigurasi.tex`

| Line | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| 6 | `DI UNIVERSITAS XYZ` | `DONE` | — |
| 9 | `DI UNIVERSITAS XYZ` | `DONE` | — |
| 12 | `AT XYZ UNIVERSITY` | `DONE` | — |
| 13 | `DI UNIVERSITAS XYZ` | `DONE` | — |
| 26 | `muhammad.afdolli@binus.ac.id` | `DECIDE` | Real email — redact or keep? |
| 35–36 | `School of Computer Science` | `DONE` | — (no BINUS string) |

---

### `sampul.tex` / `sampul2.tex`

| Line | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| 37 (sampul) / 40 (sampul2) | `Universitas Bina Nusantara` | `REPLACE` | `Universitas XYZ` |

---

### `judul_dalam.tex`

| Line | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| 14 | `BINUS UNIVERSITY` | `REPLACE` | `XYZ UNIVERSITY` |

---

### `abstrak.tex`

| Line | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| 9 | `UNIVERSITAS BINA NUSANTARA` | `REPLACE` | `UNIVERSITAS XYZ` |
| 32 | `XYZ University` (×2) | `DONE` | — |
| 49 | `XYZ University` (×2) | `DONE` | — |

---

### `orisinalitas.tex`

| Line | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| 6 | `Universitas Bina Nusantara` | `REPLACE` | `Universitas XYZ` |

---

### `pernyataan.tex`

| Line | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| 21 | `Universitas Bina Nusantara` | `REPLACE` | `Universitas XYZ` |

---

### `pengantar.tex`

| Line | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| 15 | `Rektor Universitas Bina Nusantara Periode 2023 – 2028` | `REPLACE` | `Rektor Universitas XYZ Periode 2023 – 2028` |
| 16 | `Direktur Universitas Bina Nusantara Bandung` | `REPLACE` | `Direktur Universitas XYZ Bandung` |
| 17 | `Head of Department of Computer Science, Universitas Bina Nusantara Bandung` | `REPLACE` | `Head of Department of Computer Science, Universitas XYZ Bandung` |
| 18 | `Ketua Program Studi Teknik Informatika, Universitas Bina Nusantara Bandung` | `REPLACE` | `Ketua Program Studi Teknik Informatika, Universitas XYZ Bandung` |

---

### `bab1.tex`

| Line | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| 7 | `Universitas XYZ` | `DONE` | — |
| 11 | `XYZ University` | `DONE` | — |
| **18** | **`Bina Nusantara University`** (figure caption) | **`REPLACE`** | **`XYZ University`** — ⚠ only real-name leak in body |
| 20 | `\cite{binusmaba2021}` … `\cite{binusmaba2025}` | `DECIDE` | If citation keys are renamed in ref.bib, update all `\cite{}` keys here too |
| 23, 27, 29, 52, 107, 113 | `XYZ University` / `XYZ` | `DONE` | — |

---

### `bab3.tex`

| Line | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| 370, 2324 | `XYZ University` / `Universitas XYZ` | `DONE` | — |
| **1679** | **`binusian\_id : str`** (class diagram) | **`DECIDE`** | `xyz\_id : str` or `student\_id : str`? |
| **2011** | **`binusian\_id : String`** (ERD) | **`DECIDE`** | Same as above — must match bab3:1679 |

---

### `bab5.tex`

| Line | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| 10, 35 | `XYZ University` | `DONE` | — |

---

### `lampiran.tex`

| Line | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| **94** | `\definecolor{binusblue}{…}` | `DECIDE` | Rename macro to `xyzblue`? Also update lines 101 and 104 |
| 101 | `\arrayrulecolor{binusblue}` | `DECIDE` | → `xyzblue` if macro renamed |
| 104 | `\color{binusblue}` | `DECIDE` | → `xyzblue` if macro renamed |
| **128** | `Binusian ID : 2602139141` | `DECIDE` | `XYZ Student ID` or `XYZIAN ID` or just `Student ID`? |
| **130** | `muhammad.afdolli@binus.ac.id` | `DECIDE` | Real email — redact or keep? |
| 145 | `BINUS University` | `REPLACE` | `XYZ University` |
| 168 | `GDSC Binus` | `REPLACE` | `GDSC XYZ` |
| **204** | `Binusian ID : 2602187241` | `DECIDE` | Same as line 128 |
| **206** | `rakha.azizi@binus.ac.id` | `DECIDE` | Real email |
| 220 | `BINUS University` | `REPLACE` | `XYZ University` |
| 240 | `MCB Binus @Bandung` | `REPLACE` | `MCB XYZ @Bandung` |
| 247 | `Bina Nusantara University - IT Developer` | `REPLACE` | `XYZ University - IT Developer` |
| **274** | `Binusian ID : 2602096230` | `DECIDE` | Same as line 128 |
| **276** | `theofilus.septian@binus.ac.id` | `DECIDE` | Real email |
| 290 | `BINUS University` | `REPLACE` | `XYZ University` |
| 312 | `Bina Nusantara Computer Club @Bandung` | `REPLACE` | `XYZ Computer Club @Bandung` |
| 313 | `KMK Binus @Bandung` | `REPLACE` | `KMK XYZ @Bandung` |

---

### `ref.bib`

| Lines | Current string | Strategy | Proposed replacement |
|---|---|---|---|
| 2 | `% ref.bib — Verified citations for Skripsi BINUS` | `REPLACE` | `% ref.bib — Verified citations for Skripsi XYZ` |
| 9 | `@misc{binus2025enrichment,` | `DECIDE` | Key rename → `@misc{xyz2025enrichment,` — must also update all `\cite{binus2025enrichment}` |
| 10–11 | `author = {{BINUS University}}`, `title = {… di {BINUS University}!}` | `REPLACE` | `{XYZ University}` |
| **13** | `url = {https://binus.ac.id/…}` | **`SKIP`** | Real URL — cannot change |
| 17 | `@misc{binusmaba2021,` | `DECIDE` | Key → `@misc{xyzmaba2021,` |
| 18–19 | `author = {{BINUS University}}`, `title = {… {BINUSIAN} … {BINUS UNIVERSITY}}` | `REPLACE` | Replace display text; keep URL |
| **21** | `url = {https://binus.ac.id/…}` | **`SKIP`** | Real URL |
| 25 | `@misc{binusmaba2022,` | `DECIDE` | Key → `@misc{xyzmaba2022,` |
| 26–27 | `author`, `title` BINUS strings | `REPLACE` | `XYZ University` |
| **29** | `url` | **`SKIP`** | Real URL |
| 33 | `@misc{binusmaba2023,` | `DECIDE` | Key → `@misc{xyzmaba2023,` |
| 34–35 | `author`, `title` BINUS/BINUSIAN strings | `REPLACE` | `XYZ University` / `XYZIAN` |
| **37** | `url` | **`SKIP`** | Real URL |
| 41 | `@misc{binusmaba2024,` | `DECIDE` | Key → `@misc{xyzmaba2024,` |
| 42–43 | `author`, `title` | `REPLACE` | `XYZ University` |
| **45** | `url` | **`SKIP`** | Real URL |
| 49 | `@misc{binusmaba2025,` | `DECIDE` | Key → `@misc{xyzmaba2025,` |
| 50–51 | `author`, `title` BINUS/BINUSIAN | `REPLACE` | `XYZ University` / `XYZIAN` |
| **53** | `url` | **`SKIP`** | Real URL |

---

### `.context/Diagram/` files (context docs, not compiled into PDF)

| File | Line | String | Strategy | Proposed |
|---|---|---|---|---|
| `ClassDiagramDescription.md` | 17 | `binusian_id: str` | `DECIDE` | `xyz_id: str` or `student_id: str` |
| `class-data.mmd` | 19 | `+str binusian_id` | `DECIDE` | Must match bab3 decision |
| `ClassDiagram.md` | 17 | `+str binusian_id` | `DECIDE` | Must match bab3 decision |
| `Diagram.md` | 25 | `+String binusian_id` | `DECIDE` | Must match bab3 decision |
| `Diagram.md` | 166 | `VARCHAR_32 binusian_id` | `DECIDE` | Must match bab3 decision |

---

## Decision Points Before Editing

Before any edits run, these 4 questions need answers:

| # | Question | Options |
|---|---|---|
| D1 | **Email addresses** (`binus.ac.id` in Awal_konfigurasi + lampiran CVs) | A) Keep real emails as-is · B) Redact to `[redacted]@xyz.ac.id` |
| D2 | **`binusian_id` field name** (bab3:1679, bab3:2011, 5 diagram files) | A) Rename to `xyz_id` · B) Rename to `student_id` · C) Keep as-is |
| D3 | **`binusblue` color macro** (lampiran:94,101,104) | A) Rename to `xyzblue` · B) Keep internal macro name as-is (not visible in PDF) |
| D4 | **Citation keys** (`binus2025enrichment`, `binusmaba2021`–`2025`) | A) Rename keys to `xyz*` and update all `\cite{}` in bab1 · B) Keep keys unchanged (safe — keys are internal labels) |

> URLs in ref.bib (`binus.ac.id` links) are **always SKIP** — they are real working links required for the citation and cannot be changed without breaking the reference.

---

## Summary Counts

| Category | Count |
|---|---|
| `REPLACE` (straightforward text swap) | **22 occurrences** across 10 files |
| `DECIDE` (need user input first) | **17 occurrences** across 5 files |
| `SKIP` (real URLs — untouched) | **5 occurrences** in ref.bib |
| `DONE` (already XYZ) | **~20+ occurrences** across bab1, bab3, bab5, abstrak, Awal_konfigurasi |
