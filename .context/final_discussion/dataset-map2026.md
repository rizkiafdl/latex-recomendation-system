---
name: dataset-map2026
description: Full audit of map_2026.xlsx — the actual ground truth dataset used for evaluation in the thesis; extracted 2026-06-16
metadata:
  type: reference
---

# Dataset Audit: `map_2026.xlsx`

> Source: `.context/final_discussion/map_2026.xlsx`
> Extracted: 2026-06-16
> Purpose: Ground truth dataset for faculty supervisor recommendation evaluation

---

## File Structure (5 sheets)

| Sheet | Rows | Cols | Purpose |
|---|---|---|---|
| Sheet1 | 172 (1 header + 171 students) | 42 | **Main dataset** — all student records + `kode dosen` (ground truth label) |
| Sheet2 | 172 | 21 | EPC-2 lookup sheet — VLOOKUP against Sheet1; col A has 29 specific student IDs being verified |
| Sheet3 | 190 | 2 | FS-grouped student roster (supervisor name → student name list) |
| Sheet4 | 10 | 3 | Small lookup/cross-check (student ID → supervisor code via `=Cx` formula) |
| Sheet5 | 172 | 21 | EPC-3 lookup sheet — VLOOKUP against Sheet1; col A has 15 specific student IDs being verified |

---

## Sheet1 — Main Dataset (Primary Source)

### Columns (first 17 are actual data; cols 18–42 are formula/lookup artifacts)

| # | Column | Notes |
|---|---|---|
| 1 | BINUSIAN ID | BN-prefixed alternate student ID |
| 2 | STUDENT ID | NIM (primary key in system) |
| 3 | STUDENT NAME | Full name |
| 4 | STUDENT EMAIL | @binus.ac.id |
| 5 | STUDENT PHONE | — |
| 6 | TRACK | Enrichment track type |
| 7 | **`kode dosen`** | **= `current_supervisor_code` in the system — ground truth label** |
| 8 | FS | Faculty supervisor full name (text) |
| 9 | GPA | Cumulative GPA |
| 10 | TOTAL SKS | Credit hours completed |
| 11 | PARTNER/LECTURER | Company or lecturer name for internship/research |
| 12 | POSITION/TOPIC | Internship position or research topic |
| 13 | DURATION | Internship duration (months) |
| 14 | JOB START DATE | — |
| 15 | JOB END DATE | — |
| 16 | WORK SCHEMA | Onsite / WFO / Hybrid |
| 17 | ENROLLMENT STATUS | Enrolled / Not Yet Enrolled |

---

## Student Count & Ground Truth Coverage

| Metric | Value |
|---|---|
| Total students in dataset | **171** |
| Students WITH `kode dosen` (valid ground truth) | **168** |
| Students WITHOUT `kode dosen` (excluded from evaluation) | **3** |

### 3 Excluded Students (all Study Abroad track — no CS supervisor assigned)

| STUDENT ID | NAME | TRACK |
|---|---|---|
| 2702262072 | PRIHASTA KRISA ASADHA | Study Abroad |
| 2702361280 | AKBAR KHALIFA JAYADININGRAT | Study Abroad |
| 2702275371 | VINCENT DEVANO | Study Abroad |

---

## Supervisor Roster (14 supervisors, perfectly balanced)

| Kode Dosen | Nama | Mahasiswa |
|---|---|---|
| D1749 | Dr. Johan Muliadi Kerta, S.Kom., M.M. | 12 |
| D2211 | Dr. Abdul Haris Rangkuti, S.Kom., M.M., M.Si. | 12 |
| D5918 | Dr. Boby Siswanto, S.T., M.T. | 12 |
| D6184 | Dr. Mochammad Haldi Widianto, S.T., M.T. | 12 |
| D6274 | Dr. Husni Iskandar Pohan, S.Kom, M.T. | 12 |
| D6407 | Dr. Dany Eka Saputra, S.T., M.T. | 12 |
| D6408 | Rissa Rahmania, S.T., M.T. | 12 |
| D6469 | Muhammad Maulana Ramadhan, S.Kom., M.Kom. | 12 |
| D6532 | Dr. Dani Suandi, S.Si., M.Si. | 12 |
| D6670 | Budi Juarto | 12 |
| D6826 | Karen Etania Saputra, S.Kom., M.Kom. | 12 |
| D6836 | Riccosan, S.Kom., M.Kom. | 12 |
| D7055 | Livia Janice Widiapradja, S.Si., Ph.D. | 12 |
| D7187 | Cutifa Safitri, B.CS., M. IT., Ph.D. | 12 |
| **Total** | | **168** |

**Note:** Ground truth is perfectly balanced — exactly 12 students per supervisor. This is the EPC's manual assignment result, not a system output.

---

## Track Distribution

| Track | Count | % |
|---|---|---|
| Company Internship | 117 | 68.4% |
| Specific Independent Study | 36 | 21.1% |
| Research Fellowship | 11 | 6.4% |
| **Study Abroad** | **3** | **1.8%** (excluded — no supervisor) |
| Community Impact Internship | 1 | 0.6% |
| Entrepreneurship | 1 | 0.6% |
| Certified Research | 1 | 0.6% |
| Certified Internship | 1 | 0.6% |
| **Total** | **171** | |

---

## Multi-EPC Evidence (Sheet2 & Sheet5)

Sheet2 and Sheet5 each contain a subset of student IDs in column A with VLOOKUP formulas pointing to Sheet1's `kode dosen` column. These sheets represent **additional EPC users looking up/verifying specific assignments**:

- Sheet2 col A: 29 student IDs — one EPC's verification list
- Sheet5 col A: 15 student IDs — another EPC's verification list

**Implication for thesis writing:** The phrase "beberapa EPC" in bab1 is technically justified (multiple EPCs interacted with this file), but the ground truth label (`kode dosen`) comes from Sheet1 — a single institutional dataset submitted by EPC admin. The multi-EPC involvement is verification/cross-check, not independent labeling. Do NOT frame it as "multiple independent ground truth sources."

---

## Key Facts for Thesis Writing

1. **One dataset, two uses:** The same `map_2026.xlsx` serves as:
   - Ground truth (column `kode dosen` → `current_supervisor_code`)
   - Supervisor profile enrichment source when `extra_docs=True` (student POSITION/TOPIC and PARTNER/LECTURER data used to enrich supervisor profiles)

2. **Batch:** Batch 2026 Program Enrichment (students who joined internship ~February 2026, likely enrolled 2023)

3. **Scope:** Program studi Computer Science, XYZ University kampus Bandung only

4. **Evaluation size:** 168 students (not 171 — 3 Study Abroad excluded)

5. **Supervisor count:** 14 (not "some" — exactly 14)

6. **Balance:** Ground truth is perfectly balanced at 12 students/supervisor — this means Match Rate evaluation compares against a balanced baseline

7. **Priority supervisors in system config:** D2211, D6184, D6826, D1749 — all 4 present in this dataset
