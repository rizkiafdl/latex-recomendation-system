# Revise Index — Agregasi Feedback Sidang

> Agregasi & pemetaan feedback revisi pasca-sidang ke lokasi spesifik di naskah tesis.
> Sumber: `.context/revisi/abdul.md` (Penguji 1), `.context/revisi/rissa.md` (Penguji 2)
> Disusun: 2026-08-08

---

## Ringkasan Sumber

| Penguji | File | Bentuk Feedback |
|---|---|---|
| **Penguji 1 — Abdul** | `abdul.md` | 7 poin revisi formal terhadap naskah (fokus BAB 1 & BAB 3) |
| **Penguji 2 — Rissa** | `rissa.md` | Catatan performa lisan per mahasiswa (Theofilus, Rakha, Rizki) + 2 saran konkret untuk naskah |

**Total item revisi naskah yang dapat ditindaklanjuti: 9** (7 dari Abdul + 2 saran dari Rissa).
Catatan performa lisan per mahasiswa dicatat sebagai konteks, bukan revisi dokumen.

---

## Tabel Agregasi & Pemetaan

| # | Feedback | Penguji | Lokasi di Naskah | Jenis Revisi | Prioritas |
|---|---|---|---|---|---|
| R1 | Perjelas gambaran latar belakang, rumusan masalah, tujuan & manfaat agar **sesuai dengan scope penelitian** | Abdul #1 | **BAB 1** §1.1 Latar Belakang, §1.2 Rumusan Masalah, §1.3 Ruang Lingkup, §1.4 Tujuan & Manfaat | Konten — konsistensi antar-subbab dengan scope | Tinggi |
| R2 | Penulisan BAB 1 harus benar-benar sesuai kebutuhan penelitian & **tidak memakai bahasa bantuan AI** | Abdul #2 | **BAB 1** (seluruh `bab1.tex`) | Gaya penulisan — de-AI / humanisasi bahasa | Tinggi |
| R3 | Perbaiki **kerangka berpikir** agar sesuai judul + tambah **tabel jadwal pelaksanaan penelitian** | Abdul #3 | **BAB 3** §3.1 Kerangka Berpikir (Gambar 3.1) + **tabel baru** (jadwal penelitian) | Diagram + tabel baru | Tinggi |
| R4 | Perjelas **Analisis Kebutuhan Fungsional & Non-Fungsional** dengan menambah keterangan | Abdul #4 | **BAB 3** §3.3 (7 keb. fungsional + 4 keb. non-fungsional) | Konten — tambah keterangan/deskripsi tiap item | Sedang |
| R5 | Perjelas maksud **Tabel 3.4 Representasi Data Faculty Supervisor** — mengapa hanya pakai atribut di tabel saja | Abdul #5 | **BAB 3** Tabel 3.4 + narasi §3.4 Representasi Data | Konten — justifikasi pemilihan atribut | Sedang |
| R6 | Perbaiki & perjelas **Use Case Diagram** dan **Activity Diagram**, termasuk **aktor yang terlibat** | Abdul #6 | **BAB 3** §3.7 — Use Case (Gambar 3.2) + Activity Diagram (fig TikZ) | Diagram + narasi aktor | Sedang |
| R7 | Perbaiki **kesimpulan** agar mencerminkan **rumusan masalah** | Abdul #7 | **BAB 5** §5.1 Simpulan | Konten — align simpulan ↔ RQ | Tinggi |
| R8 | Tambahkan **penelitian terkait pemilihan metode transformer sebagai encoder** (justifikasi paper) | Rissa (saran 1) | **BAB 2** Landasan Teori (Transformer Embedding) + Penelitian Terkait (Tabel 2.1) | Konten — tambah sitasi/paper justifikasi | Tinggi |
| R9 | Tunjukkan **hasil similarity mahasiswa↔FS dari input atribut hingga input cosine** — perjelas vektor A & vektor B | Rissa (saran 2) | **BAB 3** §3.4–3.5 (contoh terkerja) dan/atau **BAB 4** | Konten — worked example + definisi vektor | Tinggi |

---

## Detail per Item

### R1 — Konsistensi BAB 1 dengan Scope
**Sumber:** Abdul #1
**Masalah:** Latar belakang, rumusan masalah, tujuan/manfaat belum sepenuhnya selaras dengan ruang lingkup (jalur magang Enrichment, CS, XYZ Bandung, batch 2026, peran DSS).
**Aksi:** Pastikan setiap RQ, tujuan, dan manfaat dibatasi eksplisit pada scope internship-track + peran decision support (EPC pemegang keputusan). Buang klaim yang lebih luas dari scope.
**File:** `bab1.tex` §1.1–1.4

### R2 — Hilangkan Gaya Bahasa AI di BAB 1
**Sumber:** Abdul #2
**Masalah:** Penguji menilai gaya penulisan terasa dibantu AI.
**Aksi:** Tulis ulang dengan gaya natural/akademik manusiawi, kalimat lebih spesifik ke kebutuhan penelitian, kurangi pola generik. Pertimbangkan skill `/humanize`.
**File:** `bab1.tex` (menyeluruh)
**Terkait memori:** revisi CA2 (bab1 "berbagai fakultas") — cek konsistensi.

### R3 — Kerangka Berpikir + Tabel Jadwal Penelitian
**Sumber:** Abdul #3
**Masalah:** (a) Kerangka berpikir belum selaras judul; (b) belum ada tabel jadwal pelaksanaan penelitian.
**Aksi:**
- Revisi Gambar 3.1 agar alur mencerminkan judul (semantic similarity + FS recommendation).
- Tambahkan tabel jadwal penelitian (timeline/Gantt sederhana) — **elemen baru**, belum ada di naskah.
**File:** `bab3.tex` §3.1
**Terkait memori:** `ActivityDiagramTikzRules.md` / kerangka berpikir TikZ.

### R4 — Perjelas Kebutuhan Fungsional & Non-Fungsional
**Sumber:** Abdul #4
**Masalah:** 7 keb. fungsional + 4 keb. non-fungsional hanya berupa daftar, minim keterangan.
**Aksi:** Tambah kolom/keterangan deskriptif per requirement (apa, mengapa, bagaimana dipenuhi).
**File:** `bab3.tex` §3.3

### R5 — Justifikasi Atribut Tabel 3.4 (Representasi Data FS)
**Sumber:** Abdul #5
**Masalah:** Tidak jelas mengapa representasi FS hanya memakai atribut tertentu (`profile_keywords` + `extra_docs`).
**Aksi:** Tambah narasi alasan pemilihan atribut (relevansi semantik, ketersediaan data, exclusion GPA/identitas). Jelaskan peran `extra_docs` historis.
**File:** `bab3.tex` §3.4, Tabel 3.4

### R6 — Perbaiki Use Case & Activity Diagram + Aktor
**Sumber:** Abdul #6
**Masalah:** Diagram kurang jelas; aktor perlu ditegaskan.
**Aksi:**
- Use Case (Gambar 3.2, 12 UC): pertegas aktor EPC (satu-satunya aktor manusia); klarifikasi mahasiswa/supervisor sebagai entitas data, bukan aktor.
- Activity Diagram: perjelas alur & swimlane aktor.
**File:** `bab3.tex` §3.7 + file TikZ diagram
**Terkait memori:** `UseCaseDiagramTikzRules.md`, `ActivityDiagramTikzRules.md`, `UseCaseNarrativeRevision.md`

### R7 — Simpulan Cerminkan Rumusan Masalah
**Sumber:** Abdul #7
**Masalah:** Simpulan belum eksplisit menjawab tiap RQ.
**Aksi:** Struktur ulang §5.1 agar tiap poin simpulan memetakan langsung ke RQ1/RQ2/RQ3 (sudah ada draf 4 poin — pertegas penandaan [RQ]).
**File:** `bab5.tex` §5.1

### R8 — Paper Justifikasi Transformer sebagai Encoder
**Sumber:** Rissa (saran 1) — diperkuat catatan Theofilus & Rakha
**Masalah:** Belum ada paper yang menunjukkan alasan kuat memilih transformer sebagai encoder untuk rekomendasi; pemilihan 3 model HuggingFace tanpa sitasi paper.
**Aksi:** Tambah penelitian terkait/landasan yang menjustifikasi transformer-based embedding sebagai encoder + sitasi untuk BGE-M3, Qwen3, mE5.
**File:** `bab2.tex` (Landasan Teori Transformer/Semantic Similarity + Tabel 2.1 Penelitian Terkait)
**Terkait memori:** `uml-notation-symbols` (bukan ini), lebih relevan: `embedding-models-recommendation-2026-06-13.md`, `international-similar-studies-2026-06-13.md`, `citation-audit.md`

### R9 — Worked Example Similarity (Vektor A & B)
**Sumber:** Rissa (saran 2) — diperkuat catatan Theofilus
**Masalah:** Belum ada permisalan hasil similarity; belum jelas apa vektor A (mahasiswa) dan vektor B (dosen) dari input atribut hingga input cosine.
**Aksi:** Tambah contoh terkerja: atribut mahasiswa → gabung teks → embedding (vektor A); atribut FS → dokumen → embedding (vektor B) → cosine → skor → matriks. Rizki sudah bisa menjelaskan ini secara lisan — tuangkan ke naskah.
**File:** `bab3.tex` §3.4–3.5 (contoh) dan/atau `bab4.tex` (ilustrasi hasil)

---

## Konteks: Catatan Performa Lisan per Mahasiswa (Rissa)

> Bukan revisi dokumen, tapi menunjukkan area yang naskah perlu perkuat agar penjelasan lisan lebih terdukung.

| Mahasiswa | Poin Positif | Kekurangan Saat Sidang | Implikasi ke Naskah |
|---|---|---|---|
| **Theofilus** | Jelaskan sistem rekomendasi berbasis similarity; konsep transformer & cosine baik | Belum tunjukkan alasan kuat pilih transformer (paper); belum bisa tunjukkan permisalan hasil similarity | → R8, R9 |
| **Rakha** | Jelaskan sistem rekomendasi; tunjukkan paper TF-IDF | Pilih 3 model transformer tanpa paper; **tidak bisa jelaskan konsep cosine**, bingung baca buku, tak tahu letak persamaan cosine di buku | → R8; perjelas sitasi buku persamaan cosine di `bab2.tex` |
| **Rizki** | Jelaskan basis transformer & perannya sbg encoder; jelaskan input cosine dari variabel mahasiswa & dosen; **jelaskan perhitungan cosine sangat baik + sample matriks** | — | Model penjelasan R9 (tuangkan ke naskah) |

**Catatan tambahan:** Persamaan cosine similarity di BAB 2 sebaiknya diberi sitasi buku yang jelas + nomor persamaan agar mudah dirujuk (menanggapi kesulitan Rakha).

---

## Pemetaan per Bab (Ringkas)

| Bab | Item Revisi |
|---|---|
| **BAB 1** | R1, R2 |
| **BAB 2** | R8 (+ perjelas sitasi persamaan cosine) |
| **BAB 3** | R3, R4, R5, R6, R9 |
| **BAB 4** | R9 (opsional ilustrasi hasil) |
| **BAB 5** | R7 |

---

## Urutan Kerja yang Disarankan

1. **R2 + R1** (BAB 1) — perbaikan gaya & konsistensi scope (fondasi).
2. **R8** (BAB 2) — riset & tambah paper justifikasi transformer.
3. **R3, R4, R5, R6, R9** (BAB 3) — batch diagram + tabel + worked example.
4. **R7** (BAB 5) — sinkronkan simpulan dengan RQ (setelah BAB 1 final).
