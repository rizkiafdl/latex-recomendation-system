---
name: writing-revision-16-06-2026
description: Content accuracy revision items found 2026-06-16 — factual mismatches between abstract/latar belakang claims and actual system implementation/dataset scope
metadata:
  type: project
---

# Content Revision — 2026-06-16

> Scope: `abstrak.tex`, `bab1.tex`
> Category: **CA — Content Accuracy** (factual mismatch between written claims and actual implementation/data)
> Status legend: ❌ Open | ✅ Fixed
> Dataset reference: [[dataset-map2026]] — `.context/final_discussion/dataset-map2026.md`
> Key facts (verified from `map_2026.xlsx`): 171 students total · 168 with `kode dosen` (3 Study Abroad excluded) · 14 supervisors × 12 each · single Excel source · program studi Computer Science kampus Bandung only

---

## CA1 — `abstrak.tex` lines 34 (EN) & 51 (ID): "Supervisor expertise descriptions" misrepresents the actual supervisor document source

**Severity: KRITIS**

### What the abstract claims
- English (`abstrak.tex:34`): *"textual data from student profiles and **supervisor expertise descriptions**"*
- Indonesian (`abstrak.tex:51`): *"data tekstual dari profil mahasiswa dan **deskripsi bidang keahlian Faculty Supervisor**"*

### What the system actually uses
Supervisor documents are NOT free-form expertise descriptions written by supervisors. The actual source is:
1. **`profile_keywords`** — comma-separated keyword chips manually entered by EPC via Supervisor Studio UI (not by the supervisors themselves)
2. **Auto-detected domain labels** — 19-category labels inferred from those keywords via `LABEL_TERMS` dictionary
3. **Optionally (`extra_docs=True`)** — historical student assignment data: internship positions, company names, and job descriptions of previously supervised students

The phrase "expertise descriptions" implies supervisors authored their own profiles in narrative form. This is misleading.

### Fix
Replace in both EN and ID abstract:

**English (`abstrak.tex:34`):**
- Current: `"supervisor expertise descriptions"`
- Target: `"supervisor competency keyword profiles"` *(or: "supervisor keyword-based competency profiles enriched with historical mentoring data")*

**Indonesian (`abstrak.tex:51`):**
- Current: `"deskripsi bidang keahlian Faculty Supervisor"`
- Target: `"profil kata kunci kompetensi Faculty Supervisor"` *(or: "profil kompetensi Faculty Supervisor berbasis kata kunci yang diperkaya dengan data historis bimbingan")*

**Status:** ✅ Fixed — 2026-06-17. EN: "supervisor expertise descriptions" → "supervisor competency keyword profiles"; ID: "deskripsi bidang keahlian Faculty Supervisor" → "profil kata kunci kompetensi Faculty Supervisor". EN ground truth sentence rewritten to single-source framing (institutional data, EPC, batch 2026). ID same.

---

## CA2 — `bab1.tex` lines 27 & 105: "dari berbagai fakultas" overstates the dataset scope

**Severity: KRITIS**

### What the text claims
- `bab1.tex:27`: *"Ground truth pada penelitian ini diperoleh berdasarkan hasil pencocokan manual yang dilakukan oleh beberapa \textit{Enrichment Program Coordinator} (EPC) **dari berbagai fakultas**..."*
- `bab1.tex:105`: *"...data ground truth diperoleh dari hasil pencocokan manual yang dilakukan oleh beberapa \textit{Enrichment Program Coordinator} (EPC) **dari berbagai fakultas**."*

### Contradiction within BAB 1 itself
- `bab1.tex:11`: interviews were from **"dua fakultas"** (two faculties) — for problem *discovery* ✅
- `bab1.tex:52`: scope explicitly limited to **"program studi Computer Science"** ✅
- `bab1.tex:27` and `bab1.tex:105`: imply the *evaluation dataset* spans multiple faculties ❌

### What the actual dataset is
- 171 students total (168 with valid ground truth), 14 supervisors — **all from Computer Science** (one faculty, one batch, year 2024)
- The "dua fakultas" in line 11 refers to the **interview/observation phase** (problem discovery), not the data used for evaluation
- Ground truth = `current_supervisor_code` from CS historical assignment data only

Saying "EPC dari berbagai fakultas" for the ground truth implies multi-faculty evaluation data, which is false. Reviewers who read line 52 (CS-only scope) alongside lines 27/105 will see a direct contradiction.

### Fix
**`bab1.tex:27`:**
- Current: `"...pencocokan manual yang dilakukan oleh beberapa \textit{Enrichment Program Coordinator} (EPC) dari berbagai fakultas, serta didukung oleh dokumen pemetaan historis..."`
- Target: `"...pencocokan manual yang dilakukan oleh \textit{Enrichment Program Coordinator} (EPC) pada program studi Computer Science, serta didukung oleh dokumen pemetaan historis..."`

**`bab1.tex:105`:**
- Current: `"...data ground truth diperoleh dari hasil pencocokan manual yang dilakukan oleh beberapa \textit{Enrichment Program Coordinator} (EPC) dari berbagai fakultas."`
- Target: `"...data ground truth diperoleh dari hasil pencocokan manual yang dilakukan oleh \textit{Enrichment Program Coordinator} (EPC) pada program studi Computer Science di XYZ University."`

**Status:** ✅ Fixed — 2026-06-17. bab1:27 rewritten to single-source (data institusional batch 2026 CS, kode dosen dari EPC, atribut current_supervisor_code). bab1:56 & 123 "dan data pemetaan historis" removed — now "data institusional penugasan faculty supervisor Program Enrichment". bab1:105 "beberapa EPC dari berbagai fakultas" replaced with single-source framing including current_supervisor_code.

---

## MC1 — Missing SDLC / Waterfall grounding across bab2 + bab3

**Severity: KRITIS**

### Problem

`bab3.tex:2109` contains one sentence: *"Proses implementasi dilakukan dengan mengacu pada metodologi \textit{waterfall}..."* — but:
- `bab2.tex` has **no waterfall/SDLC section** anywhere in Landasan Teori
- `bab3.tex:76` (`\section{Metodologi Penelitian}`) has **no explicit SDLC framing** — just "beberapa tahapan yang saling berurutan" (line 74) with no method name
- Without a bab2 definition, the bab3 mention is an ungrounded claim an examiner can challenge

### Solution — 4 changes required

**Change 1 — `bab2.tex`: New `\subsection{Model Waterfall}`**
- Insert after `\subsubsection{Entity Relationship Diagram}` (line 339), before `\section{Penelitian Terkait}` (line 343)
- Content: definition paragraph (Pressman \citeyear{pressman2010se}), 5-phase enumerate list, justification why waterfall fits this research (fixed scope, stable requirements, sequential dependencies)
- Label: `\label{model-waterfall}` (cross-referenced from bab3)

**Change 2 — `bab3.tex`: Waterfall intro after `\section{Metodologi Penelitian}` (line 76)**
- Insert between line 76 (`\section{Metodologi Penelitian}`) and line 78 (`\subsection{Analisis}`)
- Content: paragraph naming waterfall as the SDLC method with `\ref{model-waterfall}` cross-reference + mapping table below

| Fase Waterfall | Cakupan dalam BAB 3 |
|---|---|
| Analisis Kebutuhan | §Analisis Permasalahan, §Analisis Aktor Sistem, §Analisis Kebutuhan Sistem, §Analisis Data |
| Perancangan | §Perencanaan, §Perancangan Model Rekomendasi, §Kandidat Model, §Perancangan UML |
| Implementasi | §Tahapan Implementasi — §Implementasi dan Deployment Sistem |
| Pengujian | Pengujian Black Box (dilaksanakan pada BAB 4) |
| Pemeliharaan | Di luar cakupan penelitian prototipe |

**Change 3 — `bab3.tex:2107–2111`: Rewrite `\subsection{Tahapan Implementasi}` paragraph**
- Replace current 2 paragraphs with a waterfall-framework framing paragraph
- Frame this subsection explicitly as Fase 3 (Implementasi) of the waterfall model, following Fase 1 (Analisis) and Fase 2 (Perancangan) already documented above
- End with roadmap pointer ("rincian setiap langkah dijelaskan pada subbab berikut") — no implementation detail (that lives in the subsequent subsections)

**Change 4 — `ref.bib`: New Pressman citation**
- Add `@book{pressman2010se}` entry at end of file

> **Agent assignment note:** All content, BibTeX entries, and ready-to-paste LaTeX blocks for this fix are in:
> `.context/paper-research/waterfall-methodology-2026-06-16.md`
>
> That file contains:
> - **B1** — `\subsection{Model Waterfall}\label{sec:model-waterfall}` for `bab2.tex` (insert after line 342, before `\section{Penelitian Terkait}`)
> - **B2** — Waterfall intro paragraph + `\label{tab:waterfall-mapping}` table for `bab3.tex` (insert after line 76, before `\subsection{Analisis}`)
> - **B3** — 6 BibTeX entries for `ref.bib`: `pressman2010se`, `saravanos2023waterfall`, `pargaonkar2023sdlc`, `rao2025comparative`, `mishra2023structured`, `sinha2021agile`
>
> Cross-reference label used: `\label{model-waterfall}` / `\ref{model-waterfall}` (kebab-case, consistent with existing bab2 label convention). Applied 2026-06-16.

**Status:** ✅ Fixed

---

---

## CA3 — Ground truth described as two separate sources but is actually one dataset used twice

**Severity: KRITIS**

### Problem

Across bab1–3, ground truth is described inconsistently as if coming from two separate sources:
- "pencocokan manual yang dilakukan oleh EPC" (manual EPC matching)
- "dokumen pemetaan historis" (historical mapping documents)

This framing appears in multiple places:
- `bab1:27`: "pencocokan manual...serta didukung oleh dokumen pemetaan historis"
- `bab1:56, 99, 123`: various combinations of both phrases
- `bab3:347`: "data historis penugasan" (vague, no source)

### Actual data provenance

There is **one source**: the student dataset for batch 2026 Program Enrichment (students who joined 2023), obtained from EPC admin as institutional data (Excel import).

This single dataset serves **two different purposes**:
1. **Ground truth for evaluation** — the `current_supervisor_code` column records which supervisor was manually assigned by EPC; this is the evaluation label
2. **Supervisor profile enrichment** — when `extra_docs=True`, the same historical student-supervisor assignment pairs (internship positions, company names, job descriptions) are concatenated into supervisor profile documents

"Dokumen pemetaan historis" and "pencocokan manual EPC" are NOT two separate sources — they are two uses of the same institutional dataset. The current bab1 framing implies two independent validation sources, which overstates the ground truth robustness.

In addition, there is **no dedicated paragraph anywhere in bab1–3** that explains what `current_supervisor_code` is, where it comes from, or how it was obtained. It appears exactly once in passing at `bab3:263` inside a pipeline step description.

### Fix

**`bab1:27` — collapse two-source framing into one accurate statement:**
- Current: "pencocokan manual yang dilakukan oleh beberapa EPC dari berbagai fakultas, serta didukung oleh dokumen pemetaan historis"
- Target: "data historis penugasan faculty supervisor batch 2026 Program Enrichment program studi Computer Science, yang diperoleh dari EPC dalam bentuk data institusional yang memuat atribut penugasan aktual (`current_supervisor_code`) hasil pemetaan manual periode tersebut"

**`bab3` — add dedicated ground truth paragraph in `\subsubsection{Strategi evaluasi}` before line 347:**

Insert a paragraph that explains:
1. Dataset source: satu sumber institusional — data mahasiswa batch 2026 program studi Computer Science dari EPC
2. Ground truth field: `current_supervisor_code` — penugasan faculty supervisor hasil pemetaan manual EPC
3. Dual use: dataset yang sama digunakan sebagai label evaluasi DAN sebagai sumber pengayaan profil supervisor (`extra_docs`)
4. Scope: 171 mahasiswa, 3 dikecualikan karena tidak memiliki `current_supervisor_code` valid → 168 data evaluasi

**`bab1:56, 99, 123` — align with the new single-source framing** (minor rewording to remove "dokumen pemetaan historis" as a separate source)

**Status:** ✅ Fixed — 2026-06-17. bab1:27 dual-source collapsed to single institutional dataset (batch 2026 CS, current_supervisor_code from EPC). bab1:56 & 123 "dan data pemetaan historis" removed. bab1:105 multi-faculty claim removed; replaced with current_supervisor_code + CS scope. bab3: dedicated ground truth paragraph inserted in \subsubsection{Strategi evaluasi} — explains single source, current_supervisor_code field, dual use (evaluation + extra_docs), 171→168 scope.

---

## SD1 — `bab2.tex`: Tidak ada tabel simbol untuk diagram UML dan ERD

**Severity: SEDANG**

### Problem

Keempat subsubsection UML di bab2 (Use Case Diagram, Activity Diagram, Class Diagram, ERD) hanya memiliki paragraf definisi tanpa tabel simbol. Semua keempat diagram digunakan secara aktif di bab3, sehingga examiner dapat mempertanyakan tidak adanya penjelasan notasi di Landasan Teori.

### Validated Symbol Map (crawled from bab3 TikZ code, 2026-06-16)

#### Use Case Diagram — fig3.2 (`bab3:366–417`)
| No | Simbol | TikZ | Status |
|---|---|---|---|
| 1 | Aktor | Stick figure manual (circle + garis) | ✅ digunakan |
| 2 | Use Case | `uc/.style={ellipse, draw}` | ✅ digunakan |
| 3 | Batasan Sistem | `\draw[thick] ... rectangle` | ✅ digunakan |
| 4 | Asosiasi | `\draw ... --` garis lurus tanpa panah | ✅ digunakan |
| — | Include / Extend | — | ❌ tidak ada — 11 UC semuanya standalone |

#### Activity Diagram — `bab3:979+` (beberapa fig)
| No | Simbol | TikZ | Status |
|---|---|---|---|
| 1 | Initial Node | `\fill ... circle (0.22cm)` lingkaran penuh | ✅ digunakan |
| 2 | Action | `rectangle, rounded corners=4pt` | ✅ digunakan |
| 3 | Decision Node | `diamond, draw=black` + guard T/F | ✅ digunakan |
| 4 | Control Flow | `\draw[->]` panah | ✅ digunakan |
| 5 | Final Node | `\draw circle` + `\fill circle` (bullseye) | ✅ digunakan |
| 6 | Swimlane | Garis vertikal tebal + label EPC/Sistem | ✅ digunakan |
| — | Fork/Join | — | ❌ tidak ada (tidak ada alur paralel) |

#### Class Diagram — fig3.3a/b/c (`bab3:1560+`, 3 figure)
| No | Simbol | Fig | TikZ | Status |
|---|---|---|---|---|
| 1 | Kelas | 3.3a/b/c | `rectangle` + tabular 2–3 kompartemen | ✅ digunakan |
| 2 | Stereotype | 3.3b/c | Label `«dataclass»`, `«service»`, `«utility»`, dll. | ✅ digunakan |
| 3 | Asosiasi | 3.3a | `solid/.style={draw}` garis + multiplisitas 1/N | ✅ digunakan |
| 4 | Asosiasi Berarah | 3.3b | `arr/.style={->, >=stealth}` + label | ✅ digunakan |
| 5 | Dependency | 3.3c | `darr/.style={->, >=stealth, dashed}` + label "uses" | ✅ digunakan |
| 6 | Multiplisitas | 3.3a | Label 1/N pada ujung garis | ✅ digunakan |
| — | Agregasi/Komposisi | — | ❌ tidak ada — fig3.3b pakai arrow + label "owns" |

**⚠ Deviasi fig3.3b:** Komposisi (owns/1:N) direpresentasikan sebagai directed arrow, bukan filled diamond. Examiner bisa mempermasalahkan, tapi karena bab2 hanya menjelaskan simbol yang dipakai, tabel cukup listing 6 simbol di atas.

#### ERD — fig3.4 (`bab3:1964+`)
| No | Simbol | TikZ | Status |
|---|---|---|---|
| 1 | Entitas | `entity/.style={rectangle}` + atribut di dalam | ✅ digunakan |
| 2 | Primary Key (PK) | `\underline{\textbf{id}}` — teks tebal bergaris bawah | ✅ digunakan |
| 3 | Foreign Key (FK) | `\textit{field}` + label "(FK)" eksplisit | ✅ digunakan |
| 4 | Relasi | `rel/.style={draw, thick}` — garis tebal antar entitas | ✅ digunakan |
| 5 | Kardinalitas | Label 1/N pada ujung garis | ✅ digunakan |
| — | Atribut oval | — | ❌ — atribut di dalam kotak (bukan Chen) |
| — | Diamond relasi | — | ❌ — garis langsung (bukan Chen notation) |

**Notasi ERD:** table-based / IE — valid untuk ERD level implementasi. Bukan Chen notation.

---

### Fix

Tambahkan tabel simbol setelah paragraf definisi pada masing-masing subsubsection (`\cite{dennis2020systems}`), **hanya simbol yang benar-benar digunakan di bab3**:
- **Use Case** (`bab2` setelah baris 327) — 4 simbol: Aktor, Use Case, Batasan Sistem, Asosiasi
- **Activity** (`bab2` setelah baris 331) — 6 simbol: Initial Node, Action, Decision Node, Control Flow, Final Node, Swimlane
- **Class Diagram** (`bab2` setelah baris 335) — 6 simbol: Kelas, Stereotype, Asosiasi, Asosiasi Berarah, Dependency, Multiplisitas
- **ERD** (`bab2` setelah baris 339) — 5 simbol: Entitas, PK, FK, Relasi, Kardinalitas (catatan: notasi table-based)

> **Agent assignment note:** Semua sumber sitasi dan BibTeX entries yang dibutuhkan untuk fix ini sudah diverifikasi dan siap pakai di:
> `.context/paper-research/uml-notation-symbols-2026-06-16.md`
>
> File tersebut berisi:
> - **Citation map** — rekomendasi paper per diagram type
> - **Primary citation:** `\cite{dennis2020systems}` — Dennis, Wixom, Tegarden. *Systems Analysis and Design: An Object-Oriented Approach with UML*, 6th ed., Wiley, 2020. ISBN 9781119559917. **Gunakan untuk semua 4 tabel simbol** (tidak ada DOI — citation by ISBN only).
> - **Supplementary citations (semua DOI verified):**
>   - Use Case + Activity: `\cite{maatuk2021generatinguml}` — DOI `10.1145/3460620.3460768`, ACM 2021, 38 citations
>   - Activity + Class: `\cite{metzner2024umlteaching}` — DOI `10.1109/CSEET62301.2024.10663036`, IEEE 2024, free PDF di arXiv:2410.17849
>   - Class Diagram: `\cite{debari2024evaluatinguml}` — DOI `10.1145/3674805.3690741`, ACM/IEEE ESEM 2024, Gold OA
>   - ERD: `\cite{skavantzos2025ergraphs}` — DOI `10.1145/3709690`, ACM SIGMOD 2025
> - **Combined BibTeX block** siap paste ke `ref.bib`
>
> Format tabel simbol per subsubsection mengikuti pola LaTeX:
> ```latex
> \begin{table}[H]
> \centering
> \caption{Simbol Use Case Diagram}
> \begin{tabular}{|c|l|l|}
> \hline
> \textbf{No} & \textbf{Simbol} & \textbf{Keterangan} \\
> \hline
> ... & ... & ... \\
> \hline
> \end{tabular}
> \end{table}
> ```
> Gunakan `\label{tab:simbol-usecase}`, `\label{tab:simbol-activity}`, `\label{tab:simbol-class}`, `\label{tab:simbol-erd}` untuk cross-reference.
>
> Applied 2026-06-16.

**Status:** ✅ Fixed — 2026-06-17. 4 symbol tables inserted in `bab2.tex` after each subsubsection paragraph (after lines 327, 331, 335, 339). Citation: `\cite{dennis2020systems}` on all 4 tables. Labels: `tab:simbol-usecase`, `tab:simbol-activity`, `tab:simbol-class`, `tab:simbol-erd`.

---

## SD2 — `bab3.tex` fig3.2: Use Case "Export Supervisor Config" tidak ada di UC diagram

**Severity: MINOR**

### Problem

Validasi UC diagram (fig3.2) terhadap route list di `feature-system-en.md` menemukan satu route yang diimplementasikan tetapi tidak muncul di diagram:

| Route | Feature | Status di UC |
|---|---|---|
| `/supervisors/export` | Export Supervisor Config (2 sheet: supervisor_config + track_reference) | ❌ tidak ada |

11 UC lainnya sudah ter-cover. Route internal API (`/api/model-status`, `/api/supervisors`) dan stub (`/benchmark`) sengaja dihilangkan — wajar untuk UC diagram.

### Fix

Tambahkan UC ke-12 ke fig3.2 (`bab3:386–402`):

1. Node baru setelah `uc11`:
   ```latex
   \node (uc12) [uc] at (6.5, -16.5) {Export Konfigurasi Supervisor};
   ```
2. Tambahkan `uc12` ke `\foreach` loop:
   ```latex
   \foreach \uc in {uc1, uc2, uc3, uc4, uc5, uc6, uc7, uc8, uc9, uc10, uc11, uc12} {
   ```
3. Perluas system boundary (dari `-16.2` → `-18.0`):
   ```latex
   \draw[thick] (1.0, 1.0) rectangle (10.5, -18.0);
   ```

Tidak perlu menggeser UC lain.

**Status:** ✅ Fixed — 2026-06-17. Added `uc12` at `(6.5, -16.5)`, extended boundary to `-18.0`, updated foreach to include `uc12`, repositioned actor centre from `(-0.8, -7.5)` to `(-0.8, -8.25)` (12-UC vertical centre), updated all actor coordinates and fan line origin to `(-0.28, -7.60)`. Context file `.context/Diagram/UseCaseDiagramTikzRules.md` updated.

---

## LT1 — `bab2.tex`: Tidak ada landasan teori untuk algoritma greedy (solver penugasan)

**Severity: SEDANG**

### Problem

Stage 6 dari pipeline sistem (`_solve_assignment` di `recommender.py`) menggunakan **two-phase greedy algorithm** dengan capacity constraints sebagai solver penugasan mahasiswa ke faculty supervisor. Ini adalah komponen algoritmik utama yang berbeda dari NLP/embedding.

Namun `bab2.tex` tidak memiliki subsection apapun yang melandasi greedy algorithm secara teoritis. Saat ini Landasan Teori hanya mencakup:
- Representasi teks, embedding, similarity → ✅ ada
- Evaluasi metrik → ✅ ada
- Teknologi pendukung → ✅ ada
- UML + Waterfall → ✅ ada (MC1 sudah fixed)

**Yang belum ada:** teori algoritma greedy sebagai dasar solver Stage 6.

Tanpa fondasi ini, examiner dapat mempertanyakan: *"Apa justifikasi teoritis untuk pendekatan greedy dalam penugasan kapasitas?"* — dan bab2 tidak memiliki jawaban.

### Context: Apa yang diimplementasikan

Solver bekerja dalam dua fase greedy:
1. **Init:** tiap mahasiswa di-assign ke `argmax(score_matrix[i, :])` — pilihan lokal terbaik
2. **Phase 1 — Reduce Overfull:** pindahkan mahasiswa dari supervisor over-capacity; pilih pemindahan dengan *minimum score penalty*
3. **Phase 2 — Fill Underfull:** pindahkan dari donor (count > min) ke under-capacity target; pilih *minimum score penalty*

Ini adalah **greedy heuristic**, bukan Dynamic Programming. Trade-off yang disengaja: kecepatan O(N×M×k) vs. optimasi global (Hungarian Algorithm O(n³) / ILP). Trade-off ini perlu diakui dan dijustifikasi di bab2.

### Fix

**`bab2.tex`: Tambah `\subsection{Algoritma Greedy}` sebelum `\section{Penelitian Terkait}`**

Insert setelah `\subsection{Model Waterfall}` (bab2:429), sebelum `\section{Penelitian Terkait}` (bab2:445).

Konten yang dibutuhkan (3–4 paragraf):
1. **Definisi algoritma greedy** — membuat pilihan lokal optimal di setiap langkah dengan harapan mencapai solusi global yang baik; cite `\cite{duvignau2023greediness}` untuk theoretical bounds pada bipartite assignment
2. **Assignment Problem context** — masalah penugasan N mahasiswa ke M supervisor dengan kapasitas adalah varian dari weighted bipartite matching; solusi eksak (Hungarian Algorithm) O(n³); cite `\cite{ramotsisi2022optimization}` untuk optimization framing
3. **Greedy sebagai heuristic yang valid** — untuk skala kecil (N≈170, M=14) greedy two-phase cukup karena konvergen cepat dan hasil dapat diverifikasi deterministik; cite `\cite{maashi2020greedy}` sebagai precedent langsung (greedy linear heuristic untuk student-project assignment di universitas)
4. **Kaitan ke sistem** — diimplementasikan sebagai solver Stage 6 dalam pipeline rekomendasi; objective score (sum of final_scores) memungkinkan perbandingan kualitas antar run

**Citation yang direkomendasikan (semua verified via DOI):**

| Priority | BibTeX key | Paper | DOI Status |
|---|---|---|---|
| PRIMARY | `maashi2020greedy` | Maashi (2020) — Greedy linear heuristic, student-project KSU | ✅ `10.21786/bbrc/13.3/27` |
| PRIMARY | `duvignau2023greediness` | Duvignau et al. (2023) — Greedy bounds, bipartite assignment | ✅ `10.1016/j.procs.2023.08.212` Diamond OA |
| SECONDARY | `ramotsisi2022optimization` | Ramotsisi et al. (2022) — Optimization model, student-to-supervisor | ✅ `10.1155/2022/9415210` Gold OA |
| VERIFY FIRST | `simsek2021decision` | Şimşek (2021) — DSS for student-supervisor allocation | ⚠️ `10.1016/j.eswa.2021.116068` — title-search only, manual verify |

> **Agent assignment note:** Full paper metadata, abstracts, OA status, and ready-to-paste BibTeX entries are in:
> `.context/paper-research/greedy-assignment-algorithm-2026-06-17.md`
>
> That file contains:
> - **5 enriched paper records** with DOI verification status
> - **Recommended citation strategy** for `\subsection{Algoritma Greedy}`
> - **BibTeX block** ready to paste into `ref.bib`: `maashi2020greedy`, `duvignau2023greediness`, `ramotsisi2022optimization`, `simsek2021decision`, `ayegba2025structural`
> - **Exclusion note** for Joshi & Jadav (no DOI, year unknown)
>
> Do NOT use `cormen2009algorithms` — Cormen 2009 is a valid source but year is outside the BINUS 2020+ recency preference. The three verified papers above cover the same theoretical ground with recent, domain-specific examples.

**Label subsection:** `\label{algoritma-greedy}` (konsisten dengan konvensi kebab-case bab2)

**Status:** ✅ Fixed — 2026-06-17. `\subsection{Algoritma Greedy}\label{algoritma-greedy}` inserted in `bab2.tex` after `\subsection{Model Waterfall}`, before `\section{Penelitian Terkait}`. 4 paragraphs: (1) greedy paradigm + bipartite bounds `\cite{duvignau2023greediness}`; (2) assignment problem + O(n³) vs heuristic framing `\cite{ramotsisi2022optimization}`; (3) greedy validity at small scale `\cite{maashi2020greedy}`; (4) two-phase solver implementation in pipeline Stage 6. BibTeX entries `maashi2020greedy`, `duvignau2023greediness`, `ramotsisi2022optimization` added to `ref.bib`.

---

## Summary Table

| # | File | Line(s) | Issue | Severity | Status |
|---|------|---------|-------|----------|--------|
| CA1 | abstrak.tex | 34 (EN), 51 (ID) | "Supervisor expertise descriptions" misrepresents keyword+historical source | KRITIS | ✅ Fixed |
| CA2 | bab1.tex | 27, 105 | "dari berbagai fakultas" overstates dataset scope; actual data is CS-only | KRITIS | ✅ Fixed |
| CA3 | bab1.tex, bab3.tex | bab1:27,56,99,123; bab3:347 | Ground truth split into two sources but is actually one dataset (batch 2026 CS) used twice — no dedicated explanation of `current_supervisor_code` anywhere in bab1–3 | KRITIS | ✅ Fixed |
| MC1 | bab2.tex, bab3.tex, ref.bib | bab2:340, bab3:76, bab3:2109 | Waterfall claimed in bab3 with no bab2 definition and no SDLC framing in bab3 intro | KRITIS | ✅ Fixed |
| SD1 | bab2.tex | 327, 331, 335, 339 | Tidak ada tabel simbol untuk Use Case, Activity, Class Diagram, ERD di Landasan Teori | SEDANG | ✅ Fixed |
| SD2 | bab3.tex | fig3.2 (bab3:386–402) | UC "Export Konfigurasi Supervisor" (`/supervisors/export`) tidak ada di UC diagram — 11/12 route ter-cover | MINOR | ✅ Fixed |
| LT1 | bab2.tex, ref.bib | bab2: setelah Model Waterfall, sebelum Penelitian Terkait | Tidak ada landasan teori algoritma greedy — solver Stage 6 (`_solve_assignment`) pakai two-phase greedy tapi tidak ada subsection di Landasan Teori | SEDANG | ✅ Fixed |
