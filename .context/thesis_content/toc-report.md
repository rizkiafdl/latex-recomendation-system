# Table of Contents Report — Skripsi.pdf
> Extracted from `build/Skripsi.pdf` on 2026-06-14

---

## DAFTAR ISI (Table of Contents)

| Section | Title | Page |
|---------|-------|------|
| — | Kata Pengantar | vii |
| — | Abstrak / Abstract | viii |
| — | Daftar Isi | xv |
| — | Daftar Gambar | xvi |
| — | Daftar Tabel | xviii |
| **BAB I** | **PENDAHULUAN** | **1** |
| 1.1 | Latar Belakang | 1 |
| 1.2 | Rumusan Masalah | 3 |
| 1.3 | Ruang Lingkup Penelitian | 4 |
| 1.4 | Tujuan dan Manfaat | 4 |
| 1.4.1 | Tujuan | 4 |
| 1.4.2 | Manfaat | 5 |
| 1.5 | Metode Penelitian | 5 |
| 1.5.1 | Pengumpulan Data | 6 |
| 1.5.2 | Praposes Data | 6 |
| 1.5.3 | Pengembangan Sistem Rekomendasi | 6 |
| 1.5.4 | Pengembangan Prototipe Sistem | 7 |
| 1.5.5 | Evaluasi Sistem | 7 |
| 1.6 | Sistematika Penulisan | 7 |
| **BAB II** | **TINJAUAN PUSTAKA** | **9** |
| 2.1 | Landasan Teori | 9 |
| 2.1.1 | Sistem Rekomendasi | 9 |
| 2.1.2 | Artificial Intelligence | 9 |
| 2.1.3 | Machine Learning | 10 |
| 2.1.4 | Deep Learning | 10 |
| 2.1.5 | Natural Language Processing | 10 |
| 2.1.6 | Text Mining | 11 |
| 2.1.7 | Text Preprocessing | 11 |
| 2.1.8 | Representasi Teks | 12 |
| 2.1.9 | TF-IDF | 12 |
| 2.1.10 | Word Embedding | 13 |
| 2.1.11 | Transformer-Based Embedding | 14 |
| 2.1.12 | Bert Embedding | 14 |
| 2.1.13 | Semantic Similarity | 15 |
| 2.1.14 | Cosine Similarity | 15 |
| 2.1.15 | Text Embedding & Model Embedding | 16 |
| 2.1.16 | BGE-M3 (BAAI/bge-m3) | 16 |
| 2.1.17 | Qwen3-Embedding-0.6B | 17 |
| 2.1.18 | Multilingual E5-Large-Instruct (intfloat/multilingual-e5-large-instruct) | 17 |
| 2.1.19 | Evaluasi Sistem Rekomendasi | 17 |
| 2.1.20 | Hit@K | 18 |
| 2.1.21 | Normalized Discounted Cumulative Gain (nDCG@K) | 18 |
| 2.1.22 | Recall@K | 19 |
| 2.1.23 | Precision@K | 19 |
| 2.1.24 | Mean Reciprocal Rank (MRR) | 19 |
| 2.1.25 | Cosine Similarity Score | 20 |
| 2.1.26 | Assignment match rate | 20 |
| 2.1.27 | Teknologi Pendukung Sistem | 21 |
| 2.1.27.1 | Python | 21 |
| 2.1.27.2 | JavaScript | 21 |
| 2.1.27.3 | Flask + Jinja2 | 22 |
| 2.1.27.4 | Pytorch | 22 |
| 2.1.27.5 | SQLite | 23 |
| 2.1.27.6 | SQLAlchemy | 23 |
| 2.1.27.7 | Sentence-Transformers | 24 |
| 2.1.28 | Post Forwarding | 24 |
| 2.1.29 | On-Premises Deployment | 24 |
| 2.1.30 | Unified Modeling Language | 25 |
| 2.1.30.1 | Usecase Diagram | 25 |
| 2.1.30.2 | Activity Diagram | 26 |
| 2.1.30.3 | Class Diagram | 26 |
| 2.1.30.4 | Entity Relationship Diagram | 26 |
| 2.2 | Penelitian Terkait | 27 |
| **BAB III** | **METODE PENELITIAN** | **31** |
| 3.1 | Kerangka Berpikir | 31 |
| 3.2 | Metodologi Penelitian | 33 |
| 3.2.1 | Analisis | 33 |
| 3.2.2 | Analisis Permasalahan | 33 |
| 3.2.3 | Analisis Permasalahan ⚠️ *duplicate section name* | 34 |
| 3.2.4 | Analisis Kebutuhan Sistem | 34 |
| 3.2.4.1 | Kebutuhan Fungsional | 34 |
| 3.2.4.2 | Kebutuhan Non-Fungsional | 35 |
| 3.2.5 | Analisis Data | 35 |
| 3.2.6 | Perencanaan | 36 |
| 3.2.7 | Perancangan Model Rekomendasi Berbasis Semantic Similarity | 37 |
| 3.2.7.1 | Konsep Dasar Model Rekomendasi | 37 |
| 3.2.7.2 | Pembentukan Representasi Data Faculty Supervisor | 38 |
| 3.2.7.3 | Pemrosesan Data Mahasiswa | 39 |
| 3.2.7.4 | Alur Pemrosesan Data | 40 |
| 3.2.8 | Kandidat Model Text Embedding | 42 |
| 3.2.9 | Qwen3-Embedding-0.6B | 42 |
| 3.2.10 | BAAI/bge-m3 | 42 |
| 3.2.11 | intfloat/multilingual-e5-large-instruct | 43 |
| 3.2.12 | Keterkaitan Kandidat Model dengan Mekanisme Rekomendasi | 43 |
| 3.2.13 | Mekanisme Perhitungan dan Perangkingan Rekomendasi | 44 |
| 3.2.13.1 | Validasi Slot Bimbingan faculty supervisor | 45 |
| 3.2.13.2 | Strategi evaluasi | 46 |
| 3.2.14 | Perancangan UML | 47 |
| 3.2.15 | Use Case Diagram | 47 |
| 3.2.16 | Use Case Narative | 48 |
| 3.2.17 | Activity Diagram | 68 |
| 3.2.18 | Class diagram | 76 |
| 3.2.19 | Entity Relationship Diagram | 78 |
| 3.2.20 | Tahapan Implementasi | 79 |
| 3.2.21 | Implementasi Pengolahan Data | 80 |
| 3.2.22 | Implementasi Praproses Data | 80 |
| 3.2.23 | Implementasi Representasi Teks | 81 |
| 3.2.24 | Implementasi Perhitungan Semantic Similarity | 81 |
| 3.2.25 | Implementasi Sistem Rekomendasi | 81 |
| 3.2.26 | Implementasi dan Deployment Sistem | 82 |
| **BAB IV** | **HASIL DAN PEMBAHASAN** | **83** |
| 4.1 | Testing Environment | 83 |
| 4.2 | Hasil | 84 |
| 4.2.1 | Evaluasi Model | 84 |
| 4.2.2 | Hasil Analisis Model dan Pemilihan Model Akhir | 86 |
| 4.2.3 | Analisis Pengaruh Parameter Konfigurasi | 87 |
| 4.2.3.1 | Pengaruh Parameter extra_docs | 87 |
| 4.2.3.2 | Pengaruh Parameter group_bonus | 88 |
| 4.2.3.3 | Konfigurasi Terbaik | 89 |
| 4.2.4 | Distribusi Beban Pembimbing | 89 |
| 4.2.5 | Distribusi Peringkat Penugasan | 90 |
| 4.2.6 | Aplikasi Web | 91 |
| 4.2.6.1 | Login Page | 91 |
| 4.2.6.2 | Register Page | 92 |
| 4.2.6.3 | Dashboard | 92 |
| 4.2.6.4 | Data Center | 93 |
| 4.2.6.5 | Generate New Run | 93 |
| 4.2.6.6 | Run History | 94 |
| 4.2.6.7 | Run Detail | 95 |
| 4.2.6.8 | Run Recommendations | 95 |
| 4.2.6.9 | Supervisor Studio | 96 |
| 4.2.6.10 | Logout | 97 |
| 4.3 | Evaluasi | 97 |
| 4.3.1 | Login Page | 97 |
| 4.3.2 | Register Page | 98 |
| 4.3.3 | Dashboard | 99 |
| 4.3.4 | Data Center | 101 |
| 4.3.5 | Generate New Run | 103 |
| 4.3.6 | Run History | 104 |
| 4.3.7 | Supervisor Studio | 104 |
| 4.3.8 | Run Detail | 106 |
| 4.3.9 | Logout | 108 |
| **BAB V** | **SIMPULAN DAN SARAN** | **109** |
| 5.1 | Simpulan | 109 |
| 5.2 | Saran | 110 |

---

## DAFTAR GAMBAR (List of Figures)

| No. | Caption | Page |
|-----|---------|------|
| 1.1 | Pertumbuhan Jumlah Mahasiswa XYZ University Tahun 2021–2024 | 2 |
| 3.1 | Alur Kerangka Berpikir | 31 |
| 3.2 | Use Case Diagram Sistem Rekomendasi Faculty Supervisor | 48 |
| 3.3 | Activity Diagram Login | 68 |
| 3.4 | Activity Diagram Register | 69 |
| 3.5 | Activity Diagram Import Data Mahasiswa | 70 |
| 3.6 | Activity Diagram Kelola Data Faculty Supervisor | 71 |
| 3.7 | Activity Diagram Kelola Keywords Supervisor | 72 |
| 3.8 | Activity Diagram Trigger Proses Rekomendasi | 73 |
| 3.9 | Activity Diagram Lihat Hasil Rekomendasi | 74 |
| 3.10 | Activity Diagram Export Hasil ke Excel | 75 |
| 3.11 | Class Diagram: ORM Models (id = PK, italic = FK) | 77 |
| 3.12 | Class Diagram: Runtime Dataclasses | 78 |
| 3.13 | Class Diagram: Service Architecture ⚠️ *listed out of order in PDF (appears before 3.11/3.12)* | 76 |
| 3.14 | Entity Relationship Diagram (id = PK, italic = FK) | 79 |
| 4.1 | Tampilan Halaman Login | 91 |
| 4.2 | Tampilan Halaman Register | 92 |
| 4.3 | Tampilan Halaman Dashboard | 92 |
| 4.4 | Tampilan Halaman Data Center | 93 |
| 4.5 | Tampilan Modal Generate New Run | 93 |
| 4.6 | Tampilan Halaman Run History | 94 |
| 4.7 | Tampilan Halaman Run Detail | 95 |
| 4.8 | Tampilan Halaman Run Recommendations | 95 |
| 4.9 | Tampilan Halaman Supervisor Studio | 96 |
| 4.10 | Tampilan Halaman Logout | 97 |

---

## DAFTAR TABEL (List of Tables)

| No. | Caption | Page |
|-----|---------|------|
| 2.1 | Tabel Penelitian Terkait | 29 |
| 3.1 | Analisis Kebutuhan Fungsional | 35 |
| 3.2 | Analisis Kebutuhan Non-Fungsional | 35 |
| 3.3 | Representasi Data Faculty Supervisor | 39 |
| 3.4 | Pemrosesan Data Mahasiswa | 40 |
| 3.5 | Struktur Data Knowledge Base | 45 |
| 3.6 | Use Case Register | 49 |
| 3.7 | Use Case Login | 50 |
| 3.8 | Use Case Import Data Mahasiswa | 52 |
| 3.9 | Use Case Kelola Data Faculty Supervisor | 53 |
| 3.10 | Use Case Kelola Keywords Supervisor | 54 |
| 3.11 | Use Case Trigger Proses Rekomendasi | 56 |
| 3.12 | Use Case Lihat Riwayat Run | 59 |
| 3.13 | Use Case Lihat Hasil Rekomendasi | 60 |
| 3.14 | Use Case Export Hasil ke Excel | 62 |
| 3.15 | Use Case Logout | 64 |
| 3.16 | Use Case Lihat Dashboard | 66 |
| 4.1 | Spesifikasi Perangkat Keras | 83 |
| 4.2 | Spesifikasi Perangkat Lunak | 84 |
| 4.3 | Perbandingan Metrik Evaluasi Antar Model | 85 |
| 4.4 | Rata-rata Skor Kemiripan Antar Model | 86 |
| 4.5 | Pengaruh Parameter extra_docs terhadap %Rank-1 | 88 |
| 4.6 | Pengaruh Parameter group_bonus terhadap %Rank-1 | 88 |
| 4.7 | Metrik Konfigurasi Terbaik (Run 8: bge-m3, extra_docs=True, group_bonus=False) | 89 |
| 4.8 | Distribusi Beban Penugasan Pembimbing (identik pada seluruh 18 konfigurasi) | 89 |
| 4.9 | Distribusi Peringkat Penugasan (Pooled, 3.078 slot dari 18 konfigurasi) | 90 |
| 4.10 | Skenario Login Page | 98 |
| 4.11 | Skenario Register Page | 99 |
| 4.12 | Skenario Dashboard | 100 |
| 4.13 | Skenario Data Center | 102 |
| 4.14 | Skenario Generate New Run | 103 |
| 4.15 | Skenario Run History | 104 |
| 4.16 | Skenario Supervisor Studio | 105 |
| 4.17 | Skenario Run Detail | 107 |
| 4.18 | Skenario Logout | 108 |
| A.1 | Jadwal Kegiatan Penelitian | 117 |
| B.1 | Daftar Tautan Pendukung Penelitian | 118 |

---

## Issues & Anomalies Found

| # | Location | Issue |
|---|----------|-------|
| 1 | BAB III, 3.2.2 & 3.2.3 | **Duplicate section title** — both are labeled "Analisis Permasalahan" (pp. 33–34). One should likely be "Analisis Kebutuhan" or similar. |
| 2 | DAFTAR GAMBAR, Fig 3.13 | **Out-of-order listing** — Fig 3.13 (Service Architecture, p. 76) is listed before Fig 3.11 (p. 77) and 3.12 (p. 78) in the figure list, even though 3.13 appears on an earlier page. Reorder in LaTeX source. |
| 3 | Kata Pengantar | **Placeholder "XXX"** remains in the Kata Pengantar body text ("selesainya XXX dengan judul…" and "dalam mengerjakan XXX ini"). Must be replaced with "Skripsi" before submission. |
| 4 | pengesahan.tex, line 56 | Pembimbbing name in minipage uses `\underline{\pembimbingSatu}` — not wrapped in `\makebox`, which may cause Overfull \hbox if name is long. Consider `\underline{\makebox[5.5cm][c]{\pembimbingSatu}}`. |

---

## Summary Stats

- **Total named sections in TOC:** 82 entries
- **Chapters:** 5 (BAB I–V)
- **Figures:** 24 (Fig 1.1, 3.1–3.14, 4.1–4.10)
- **Tables:** 29 (Tab 2.1, 3.1–3.16, 4.1–4.18, A.1, B.1)
- **Appendices referenced:** A (Jadwal), B (Daftar Tautan)
- **Total pages (last table entry):** ~118
