---
name: citation-audit
description: Audit lengkap masalah sitasi BAB 2 — meliputi ref.bib kosong, tidak ada \cite{}, tiga paper tidak terverifikasi, dan tiga baris longtable Penelitian Terkait dengan hasil palsu.
metadata:
  type: project
---

# Citation Audit — BAB 2 & Daftar Pustaka

> Dibuat 2026-06-07. Severity: KRITIS — Daftar Pustaka saat ini akan kosong saat dikompilasi.

---

## Problem 1 — `ref.bib` Kosong (KRITIS)

**File:** `ref.bib`

`ref.bib` hanya berisi entry template dari tahun 1993 (placeholder bawaan LaTeX) dan dua entry tidak relevan (`deepfake-1`, `refactoring-1`). **Tidak ada satu pun entry yang sesuai dengan sitasi yang digunakan dalam thesis.**

`Skripsi.tex` line 185–186 memanggil:
```latex
\bibliographystyle{apacite}
\bibliography{ref}
```

Akibatnya: **Daftar Pustaka di PDF akan kosong total.**

---

## Problem 2 — Tidak Ada `\cite{}` di Seluruh Thesis (KRITIS)

**File:** `bab2.tex` (dan kemungkinan bab lain)

Seluruh sitasi ditulis sebagai teks inline dalam format APA, misalnya:
- `Mikolov et al. (2013)`
- `Devlin et al. (2019)`
- `Chen et al. (2024)`

**Tidak ada satu pun perintah `\cite{}` yang digunakan.** Ini berarti bahkan jika ref.bib diisi lengkap, sitasi tidak akan terhubung ke entri bibliografi.

**Asal masalah:** Thesis ini dimigrasikan dari Word/Google Docs via pandoc — gaya sitasi inline dipertahankan sebagai plain text, bukan dikonversi ke `\cite{}`.

---

## Problem 3 — Tiga Paper Tidak Terverifikasi di Tabel Penelitian Terkait (KRITIS)

**File:** `bab2.tex` lines 391–393

Pencarian web terhadap ketiga paper menghasilkan nol hasil yang cocok. Ketiga paper ini **sangat mungkin hallucinated** (di-generate AI tanpa verifikasi):

| Sitasi | Judul | Status |
|--------|-------|--------|
| Rahman et al. (2023) | Supervisor Recommendation System Using Contextual Text Embeddings | ❌ Tidak ditemukan |
| Li et al. (2022) | Semantic Search-Based Supervisor Recommendation System | ❌ Tidak ditemukan |
| Zhang et al. (2021) | Academic Advisor Recommendation Using Deep Learning | ❌ Tidak ditemukan |

Indikator tambahan bahwa ini bukan paper nyata:
- Dataset dideskripsikan sebagai "Data dari perlibraryan Universitas" (typo + terlalu generik)
- Kolom **Hasil** diisi ulang dengan **judul paper itu sendiri** (copy-paste placeholder)
- Tidak ada nama jurnal, konferensi, atau DOI yang bisa dilacak

---

## Problem 4 — Tiga Baris Longtable: Kolom "Hasil" = Judul Paper (SEDANG)

**File:** `bab2.tex` lines 391–393

Kolom "Hasil" untuk ketiga paper di atas berisi copy-paste dari kolom "Judul", bukan temuan penelitian aktual. Ini terkait langsung dengan Problem 3.

---

## Daftar Seluruh Sitasi Inline di bab2.tex

Sitasi di bawah ini ditulis sebagai plain text. Keasliannya perlu diverifikasi oleh penulis.

### Kemungkinan NYATA (well-known papers, bisa dilacak):
| Sitasi | Judul / Konteks |
|--------|----------------|
| Mikolov et al. (2013) | Word2Vec |
| Pennington et al. (2014) | GloVe |
| Bojanowski et al. (2017) | FastText |
| Vaswani et al. (2017) | Attention Is All You Need (Transformer) |
| Devlin et al. (2019) | BERT |
| Manning et al. (2008) | Introduction to Information Retrieval (buku) |
| Paszke et al. (2019) | PyTorch |
| Herlocker et al. (2004) | Recall@K untuk rekomendasi |
| Mihalcea et al. (2006) | Semantic Similarity |

### Perlu Verifikasi (mungkin nyata, perlu dicek):
| Sitasi | Judul / Konteks |
|--------|----------------|
| Chen et al. (2024) | BGE-M3 |
| Wang et al. (2024) | Multilingual E5-Large-Instruct |
| Dennis et al. (2020) | UML textbook |
| Tang et Al. (2016) | A Research Topic Prediction and Supervisor Recommendation System |
| Huang et al. (2019) | Supervisor Recommendation Based on Semantic Similarity |
| Aljohani & Davis (2020) | Matching Students to Supervisors Using Text Mining |

### TIDAK TERVERIFIKASI (suspect hallucinated):
| Sitasi | Status |
|--------|--------|
| Rahman et al. (2023) | ❌ Tidak ditemukan via web search |
| Li et al. (2022) | ❌ Tidak ditemukan via web search |
| Zhang et al. (2021) | ❌ Tidak ditemukan via web search |

---

## Paper Pengganti yang Ditemukan (Nyata & Terverifikasi)

Berikut paper nyata yang bisa menggantikan tiga paper tidak terverifikasi:

### Pengganti 1 — Hairani & Mujahid (2022)
- **Judul:** Recommendations of Thesis Supervisor using the Cosine Similarity Method
- **Jurnal:** SISTEMASI: Jurnal Sistem Informasi
- **Dataset:** 113 dokumen skripsi, 90 training + 23 testing, Bumigora University
- **Metode:** TF-IDF + Cosine Similarity
- **Hasil:** Berhasil merekomendasikan 21 dari 23 dokumen skripsi dengan **akurasi 91,3%**
- **URL:** https://sistemasi.ftik.unisi.ac.id/index.php/stmsi/article/view/2003

### Pengganti 2 — Sabilillah et al. (2024)
- **Judul:** Implementasi BERT dan Cosine Similarity untuk Rekomendasi Dosen Pembimbing berdasarkan Judul Tugas Akhir
- **Jurnal:** Edumatic: Jurnal Pendidikan Informatika
- **Dataset:** 3.723 judul riset dari 63 dosen (scraping Google Scholar & ResearchGate), Universitas Dian Nuswantoro
- **Metode:** BERT + Cosine Similarity
- **Hasil:** **Akurasi 90%** dalam mengidentifikasi dosen pembimbing relevan berdasarkan keselarasan topik
- **URL:** https://e-journal.hamzanwadi.ac.id/index.php/edumatic/article/view/27791

### Pengganti 3 — Masih perlu dicari
Untuk paper ketiga, penulis perlu mencari satu paper lagi dari 2021–2023 terkait:
- Supervisor/advisor recommendation dengan deep learning atau transformer
- Ada metrik evaluasi yang jelas (accuracy/precision/recall/MRR)

---

## Rencana Resolusi

### Langkah 1 — Isi `ref.bib` (KRITIS, harus dikerjakan penulis)
Semua sitasi perlu dikonversi ke format BibTeX dan ditambahkan ke `ref.bib`. Alternatif: gunakan `apacite` dengan manual entries.

### Langkah 2 — Ganti `\cite{}` (KRITIS, bisa dikerjakan Claude setelah ref.bib diisi)
Setiap sitasi inline perlu diubah menjadi `\cite{key}` yang merujuk ke entri di ref.bib.

### Langkah 3 — Ganti 3 paper tidak terverifikasi (KRITIS)
Ganti baris Rahman et al. (2023), Li et al. (2022), Zhang et al. (2021) dengan:
- Hairani & Mujahid (2022) ✅ siap
- Sabilillah et al. (2024) ✅ siap
- Satu paper lagi (perlu dicari)

### Langkah 4 — Isi kolom Hasil
Setelah paper pengganti dipilih, tulis hasil aktual di kolom "Hasil" tabel longtable.

**Why:** Daftar Pustaka yang kosong adalah pelanggaran integritas akademik yang dapat menyebabkan thesis ditolak saat sidang.
**How to apply:** Prioritaskan Problem 1 dan 3 sebelum finalisasi dokumen.
