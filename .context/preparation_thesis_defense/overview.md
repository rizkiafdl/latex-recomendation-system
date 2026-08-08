# Thesis Defense Preparation: Complete Overview
> Crawled from source files (bab1–5.tex, abstrak.tex) — 2026-07-29  
> Untuk dibaca sebelum sidang. Semua angka diambil langsung dari LaTeX sumber.

---

## Identitas Penelitian

| Field | Value |
|---|---|
| **Judul (ID)** | Pengembangan Sistem Rekomendasi Faculty Supervisor untuk Mahasiswa Menggunakan Semantic Similarity Berbasis Website di Universitas XYZ |
| **Judul (EN)** | Development of a Faculty Supervisor Recommendation System for Students Using Website-Based Semantic Similarity at XYZ University |
| **Penulis** | Muhammad Rizki Afdolli (2602139141), Rakha Naufal Azizi (2602187241), Theofilus Adhi Septian (2602096230) |
| **Pembimbing** | Budi Juarto S.T., M.Kom (D6670); Prof. Dr. Ir. Derwin Suhartono S.Kom., MTI |
| **Program** | Computer Science — School of Computer Science, BINUS University, 2026 |

---

## Abstract (Ringkasan Satu Halaman)

**Masalah:** Pemetaan mahasiswa ke Faculty Supervisor magang di Program Enrichment XYZ University dilakukan manual oleh EPC menggunakan spreadsheet: 1–3 minggu per batch, hingga 20 revisi, tanpa kesesuaian keahlian, beban supervisor tidak merata.

**Solusi:** Sistem rekomendasi berbasis *semantic similarity* (zero-shot transformer embedding) + greedy capacity-constrained solver sebagai **Decision Support System** — EPC tetap pemegang keputusan akhir.

**Metode:**
1. Data tekstual mahasiswa (track + position\_topic + work\_schema) → embedding vektor
2. Data tekstual supervisor (profile\_keywords + extra\_docs historis bimbingan) → embedding vektor  
3. Cosine similarity → skor kemiripan semantik
4. Company group bonus (opsional) → hybrid score
5. Greedy solver 2-fase → alokasi final dengan constraint kapasitas
6. Evaluasi vs ground truth (data penugasan EPC batch 2026, 168 mahasiswa valid)

**Hasil terbaik (Run 26 — BAAI/bge-m3, extra\_docs=True):**
- MRR: **0,711** | Hit@5: **92,9%** | Match Rate: **53,0%**
- Gini distribusi beban: **0,0138** (sempurna merata)
- 56 skenario black box: **semua Pass**

---

## BAB 1 — Pendahuluan

### 1.1 Latar Belakang
- Program Enrichment XYZ University: mahasiswa didampingi Faculty Supervisor selama magang.
- Proses saat ini: manual spreadsheet oleh EPC dari **3 fakultas** (narasumber: Margareth Setiawan, Yulianto, Mila Andria Savitri — wawancara 13 Desember 2025).
- **Problem kuantitatif:** 1–3 minggu/batch, revisi hingga 20 kali, penentuan supervisor acak, beban tidak merata.
- Jumlah mahasiswa XYZ terus meningkat 2021–2025 (lihat Gambar 1.1) → pendekatan manual makin tidak efektif.
- Penelitian terdahulu: Fan et al. (2021) ML automation; Rismanto et al. (2020) TF-IDF + cosine similarity (75%) → keterbatasan: tidak menangkap semantic meaning.
- Novelty: semantic similarity via text embedding → lebih dalam dari TF-IDF.

### 1.2 Rumusan Masalah (3 RQ)
1. Bagaimana merancang dan mengembangkan sistem rekomendasi Faculty Supervisor magang berbasis semantic similarity?
2. Seberapa tinggi tingkat kesesuaian hasil rekomendasi sistem dengan ground truth dari EPC?
3. Bagaimana sistem membantu meningkatkan efisiensi proses pemetaan?

### 1.3 Ruang Lingkup
- Fokus: jalur magang (internship) Program Enrichment, XYZ Bandung, program studi Computer Science.
- Data: batch 2026, dibatasi pada data yang tersedia saat penelitian.
- Ground truth: data institusional penugasan aktual EPC (`current_supervisor_code`).
- Fitur website: dashboard, data center, run history, supervisor studio.
- **Tidak mencakup:** kebijakan akademik, evaluasi usabilitas SUS oleh EPC nyata, detail implementasi teknis mendalam.
- Pengujian: black box testing (equivalence partitioning) — hanya fungsionalitas.

### 1.4 Tujuan
1. Analisis masalah pemetaan manual.
2. Pengembangan sistem rekomendasi berbasis semantic similarity.
3. Evaluasi hasil rekomendasi vs ground truth.
4. Menghasilkan prototipe web sebagai pendukung EPC.

### 1.5 Metode Penelitian (overview)
Waterfall SDLC: analisis → perencanaan → perancangan → implementasi → pengujian.

---

## BAB 2 — Tinjauan Pustaka

### Landasan Teori (rantai konseptual)

```
AI → Machine Learning → Deep Learning → NLP → Text Mining
→ Text Preprocessing → Representasi Teks
→ TF-IDF (baseline; keterbatasan: leksikal saja)
→ Word Embedding (Word2Vec, GloVe, FastText; statis, per-kata)
→ Transformer-Based Embedding (kontekstual; Vaswani et al. 2017)
→ BERT (bidirectional; Devlin et al. 2019)
→ Semantic Similarity → Cosine Similarity
→ Model Embedding: BGE-M3, Qwen3-0.6B, mE5-large-instruct
```

**Text Preprocessing — minimalis (3 langkah saja):**
1. Case folding
2. Pembersihan karakter non-alfanumerik
3. Collapse whitespace

Alasan: model transformer (BERT-based) sudah handle morfologi/sinonim via tokenisasi subword (WordPiece/BPE). Stemming & stop word removal **dihindari** agar tidak membuang sinyal kontekstual.

### 3 Model Embedding Kandidat

| Model | Developer | Params | Context | Keunggulan |
|---|---|---|---|---|
| **BAAI/bge-m3** | BAAI | — | 8.192 token | Multi-retrieval (dense+sparse+multi-vector), 100+ bahasa |
| **Qwen3-Embedding-0.6B** | Alibaba | 600M | 32K token | Context terpanjang, MRL support, paling ringan |
| **intfloat/multilingual-e5-large-instruct** | Microsoft | ~560M | 512 token | Instruction-tuned, 90+ bahasa, default sistem |

### Metrik Evaluasi

| Metrik | Apa yang diukur |
|---|---|
| **MRR** | Seberapa cepat supervisor tepat muncul di daftar |
| **Hit@1** | Apakah supervisor tepat ada di posisi ke-1 |
| **Hit@5** | Apakah supervisor tepat ada di top-5 |
| **nDCG@5 / nDCG@10** | Kualitas urutan dengan diskon logaritmik berdasarkan posisi |
| **Avg Rank** | Rata-rata posisi supervisor ground truth dalam daftar rekomendasi |
| **Assignment Match Rate** | Proporsi penugasan akhir (post-solver) yang cocok dengan histori EPC |
| **Cosine Similarity Score** | Ukuran kemiripan vektor mentah |

### Teknologi Pendukung

| Komponen | Teknologi |
|---|---|
| Backend/AI | Python 3.12, Flask 3.1.0, Jinja2 |
| ML Framework | PyTorch |
| Database | SQLite + SQLAlchemy 2.0.49 |
| Embedding | sentence-transformers (batch encoding, L2-normalized) |
| Data Processing | pandas, numpy, openpyxl |
| Deployment | On-premises (Docker container, lokal), port forwarding untuk testing |
| Frontend | JavaScript |

### Algoritma Greedy (Teoritis)
- Citasi: Duvignau et al. (2023) bipartite assignment; Maashi (2020) greedy di university; Ramotsisi et al. (2022) optimasi penugasan mahasiswa.
- Alasan memilih greedy vs Hungarian: N≈170, M=14 → skala kecil, greedy efisien dan deterministik.

### Penelitian Terkait (6 studi — Tabel 2.1)

| Peneliti | Metode | Hasil Kunci |
|---|---|---|
| Zhang et al. (2016) | Personality Matching | +12–15% mutual satisfaction rate |
| Cohan et al. (2020) SPECTER | BERT + Citation Graph | 81–84% Macro F1 (klasifikasi paper ilmiah) |
| Nagarajan et al. (2025) | TF-IDF + BERT Hybrid | Precision 99.14%, Recall 92.48% |
| Wang et al. (2025) AVRD | Chinese BERT + SimCSE | 70% dari 170 mahasiswa menemukan pembimbing ideal |
| Sabilillah et al. (2024) | BERT + Cosine Similarity | Akurasi akhir 89.99% |
| Dasri et al. (2025) | MapReduce K-Skyband | 8× lebih cepat dari BNL, rekomendasi dua arah |

**Gap dari penelitian terdahulu:**
- Belum ada yang menerapkan BGE-M3, mE5-large-instruct, Qwen3-Embedding secara komparatif untuk sistem rekomendasi supervisor akademik Indonesia (dwibahasa).
- Belum ada integrasi DSS berbasis web dengan constraint kuota dan pemerataan beban secara institusional.
- Penelitian ini mengisi gap tersebut.

---

## BAB 3 — Metode Penelitian

### 3.1 Kerangka Berpikir (Gambar 3.1)
```
Mulai → Analisis masalah & kebutuhan → Perencanaan →
Perancangan model rekomendasi → Evaluasi →
[Memenuhi kriteria?] --Ya→ Perancangan UML → Implementasi web → Selesai
                     --Tidak→ kembali ke Perencanaan
```

### 3.2 Metodologi: Waterfall SDLC

| Fase Waterfall | Cakupan dalam BAB 3 |
|---|---|
| Analisis Kebutuhan | Analisis masalah, aktor, kebutuhan fungsional/non-fungsional, data |
| Perancangan | Perencanaan, model rekomendasi, kandidat model, UML |
| Implementasi | Tahapan implementasi & deployment |
| Pengujian | Black Box (dilaksanakan di BAB 4) |
| Pemeliharaan | Di luar cakupan penelitian prototipe |

### 3.3 Analisis Sistem

**Aktor:** EPC satu-satunya. Mahasiswa dan supervisor = entitas data, bukan aktor.

**Kebutuhan Fungsional (7):**
1. Kelola data mahasiswa (posisi kerja, perusahaan)
2. Kelola profil Faculty Supervisor
3. Proses data teks mahasiswa & supervisor
4. Identifikasi kesesuaian mahasiswa–supervisor
5. Ranking supervisor berdasarkan kesesuaian
6. Pertimbangkan ketersediaan slot bimbingan
7. Tampilkan hasil rekomendasi per mahasiswa

**Kebutuhan Non-Fungsional (4):**
1. Berbasis website
2. Antarmuka sederhana
3. Transparan dan konsisten
4. Data terstruktur

### 3.4 Representasi Data

**Data Mahasiswa → 1 vektor embedding:**
- field digunakan: `track` + `position_topic` + `work_schema` (digabung)
- field tidak digunakan sebagai teks: `partner_lecturer` (dipakai sebagai kunci group bonus), `GPA`, identitas

**Data Faculty Supervisor → 1 dokumen teks dinamis:**
- Base: `profile_keywords` (dikelola EPC di Supervisor Studio)
- Jika `extra_docs=True`: diperkaya dengan data historis bimbingan (posisi, perusahaan, deskripsi pekerjaan mahasiswa yg pernah dibimbing) + rekomendasi skripsi (topik, deskripsi, rumusan masalah, luaran, jenis skripsi)

### 3.5 Pipeline Sistem (8 Tahap)

| Tahap | Modul | Apa yang terjadi |
|---|---|---|
| 1 | `excel_io.py` | Import Excel → upsert ke tabel `students` |
| 2 | `rules.py` | Bangun dokumen teks dari DB (+ extra\_docs jika aktif) |
| 3 | `embedding.py` | Encode semua dokumen → matriks embedding N×M; fallback: SentenceTransformer → TF-IDF → token overlap |
| 4 | (hybrid) | Cosine similarity × bobot + company group bonus (jika aktif) → hybrid score matrix |
| 5 | (capacity) | Hitung capacity plan: target_min=10, target_max=12 per supervisor; adjust overflow/underflow |
| 6 | (solver) | Greedy 2-fase: inisialisasi argmax → Fase1 kurangi overfull → Fase2 isi underfull |
| 7 | (evaluasi) | Hitung MRR/Hit@K/nDCG dari 3 level: content-based, hybrid, assignment |
| 8 | (simpan) | Simpan ke `recommendation_runs` + `recommendations`; ekspor Excel (standard 3 sheet / detailed 4 sheet) |

**Company Group Bonus — syarat aktivasi:**
- Minimal 2 mahasiswa se-perusahaan
- Diversitas topik ≤ 6 token
- Selisih skor 2 dosen terbaik ≥ 0,08
- Nilai bonus menurun logaritmik seiring besar kelompok

### 3.6 Kandidat Model — Perbandingan

| | BAAI/bge-m3 | Qwen3-0.6B | mE5-large-instruct |
|---|---|---|---|
| Context | 8K token | 32K token | 512 token |
| Retrieval mode | Dense+Sparse+Multi-vector | Dense | Instruction-following dense |
| Default sistem? | Tidak | Tidak | **Ya** |
| Dipilih akhir? | **Ya** | Tidak | Tidak |

### 3.7 UML — Diagram yang Ada

**Use Case Diagram (Gambar 3.2) — 12 use case:**
1. Register
2. Login
3. Import Data Mahasiswa
4. Kelola Data Faculty Supervisor
5. Kelola Keywords Supervisor
6. Lihat Dashboard
7. Trigger Proses Rekomendasi
8. Lihat Riwayat Run
9. Lihat Hasil Rekomendasi
10. Export Hasil ke Excel
11. Logout
12. Export Konfigurasi Supervisor

**Use Case Narratives (Tabel 3.6–3.17):** detail per UC termasuk pre/post-condition, flow of events, exception conditions.

**Class Diagram (Gambar 3.3):** kelas-kelas embedding/scoring/solver/evaluasi.

**ERD (Gambar 3.4):** 7 entitas SQLite — `users`, `students`, `supervisors`, `recommendation_runs`, `recommendations`, + tabel pendukung.

### 3.8 Strategi Evaluasi
- Ground truth: `current_supervisor_code` dari data institusional batch 2026 EPC.
- 171 mahasiswa total; **168 valid** (3 Study Abroad dikecualikan — tidak ada supervisor CS).
- Evaluasi dilakukan pada 3 level: content-based score, hybrid score, assignment output.
- Model terbaik dipilih berdasarkan metrik **retrieval** (MRR, Hit@K, nDCG), bukan Match Rate semata.

---

## BAB 4 — Hasil dan Pembahasan

### 4.1 Testing Environment

**Hardware:**
| Komponen | Spesifikasi |
|---|---|
| Processor | Apple M4 Pro (12-core CPU) |
| RAM | 24 GB Unified Memory |
| GPU | Apple M4 Pro GPU (20-core) + Apple Neural Engine (16-core) |
| Storage | 1 TB SSD |

**Software:** macOS Sequoia 15, Python 3.12, Flask 3.1.0, SQLAlchemy 2.0.49 + SQLite, sentence-transformers, pandas, numpy, openpyxl, VS Code.

**Model kandidat:** BAAI/bge-m3, Qwen/Qwen3-Embedding-0.6B, intfloat/multilingual-e5-large-instruct.

### 4.2 Hasil Evaluasi Model

**18 total eksperimen** (3 model × 3 konfigurasi toggle × 2 varian kapasitas), runs 26–43.

**Perbandingan pada konfigurasi identik (extra\_docs=True, group\_bonus=False):**

| Model | Run | MRR | Hit@1 | Hit@5 | nDCG@5 | nDCG@10 | Avg Rank | Match Rate |
|---|---|---|---|---|---|---|---|---|
| **BAAI/bge-m3** | **26** | **0,711** | **0,560** | **0,929** | **0,759** | **0,779** | **2,17** | 0,530 |
| Qwen3-0.6B | 32 | 0,617 | 0,470 | 0,851 | 0,660 | 0,698 | 2,95 | 0,512 |
| mE5-large-instruct | 38 | 0,672 | 0,506 | 0,905 | 0,721 | 0,748 | 2,43 | **0,583** |
| *Random baseline* | — | ~0,23 | ~0,07 | ~0,36 | ~0,21 | — | ~7,5 | — |

**Rata-rata skor kemiripan (Tabel 4.2.2):**

| Model | Avg Sim @1 | Avg True Sim | Selisih (sebaran) |
|---|---|---|---|
| BAAI/bge-m3 | 0,674 | 0,645 | **0,030** |
| Qwen3-0.6B | 0,726 | 0,684 | **0,042** |
| mE5-large-instruct | 0,907 | 0,893 | **0,014** ← nyaris seragam |

### 4.3 Pemilihan Model — Argumen Kunci

**Kenapa bge-m3 dipilih, bukan mE5 yang Match Rate-nya lebih tinggi?**

mE5-large-instruct punya skor sangat tinggi tapi **hampir seragam** (sebaran 0,014): model menilai hampir semua pasangan "sangat mirip" → kehilangan kemampuan diskriminasi ranking. Match Rate-nya tinggi bukan karena semantik lebih baik, tapi karena alokasi lebih banyak ditentukan oleh slot kapasitas (slot-filling effect), yang kebetulan cocok dengan pola distribusi historis EPC yang memang tidak berbasis keahlian murni.

**Prinsip yang ditegaskan:** Metrik retrieval (MRR, Hit@K, nDCG) = acuan utama. Assignment Match Rate = metrik pelengkap, bisa menyesatkan jika berdiri sendiri.

**bge-m3 memenuhi:** deterministik + reproducible + unggul di semua metrik retrieval.

### 4.4 Analisis Parameter Konfigurasi

**Pengaruh `extra_docs` terhadap %Rank-1:**

| Model | extra\_docs=True | extra\_docs=False | Δ |
|---|---|---|---|
| BAAI/bge-m3 | **67,8%** | 62,0% | **+5,8 pp** → aktifkan |
| Qwen3-0.6B | 49,1% | 50,9% | -1,8 pp → nonaktifkan |
| mE5-large-instruct | 54,4% | 60,8% | **-6,4 pp** → nonaktifkan |

bge-m3 mendapat manfaat dari profil supervisor yang lebih kaya (extra\_docs). mE5-large justru terdegradasi karena dokumen tambahan merusak ruang embedding yang sudah terkalibrasi.

**Pengaruh `group_bonus`:**
- Selisih %Rank-1 antara group\_bonus=True vs False = **0 pp** pada ketiga model (ketika extra\_docs dikontrol sama).
- Bonus hanya ~0,008 — tidak cukup besar untuk menggeser urutan ranking.
- Bonus terbukti aktif secara teknis tapi tidak berpengaruh terukur pada kualitas ranking.
- *Catatan:* Dalam eksperimen batch sebelumnya, group\_bonus=True selalu disertai extra\_docs=False (variabel tidak diisolasi) → apparent gap bersifat spurious.

### 4.5 Konfigurasi Terbaik: Run 26

**BAAI/bge-m3, extra\_docs=True, group\_bonus=False**

| Metrik | Nilai |
|---|---|
| MRR | **0,711** |
| Hit@1 | **0,560** (56%) |
| Hit@5 | **0,929** (92,9%) |
| nDCG@10 | **0,779** |
| %Rank-1 | **67,8%** |
| %Top-3 | **85,4%** |
| Rata-rata Peringkat | **2,17** |

Artinya: supervisor yang dipilih EPC secara historis rata-rata muncul di **posisi ke-2** daftar rekomendasi sistem.

### 4.6 Distribusi Beban Pembimbing (Greedy Solver)

**Identik pada semua 18 konfigurasi** — model embedding tidak mempengaruhi distribusi beban.

| Statistik | Nilai |
|---|---|
| Minimum per pembimbing | 12 mahasiswa |
| Maksimum per pembimbing | 13 mahasiswa |
| Rata-rata | 12,21 |
| Standar deviasi | 0,426 |
| **Gini Coefficient** | **0,0138** |
| Pembimbing dengan 13 mhs | 3 (dari 14) |
| Pembimbing dengan 12 mhs | 11 (dari 14) |

Gini 0,0138 ≈ nol → distribusi hampir sempurna merata. **Ini menjawab langsung RQ3** tentang pemerataan beban.

### 4.7 Distribusi Peringkat Penugasan (Pooled 3.078 slot, 18 konfigurasi)

| Peringkat Ditetapkan | Jumlah | Persentase |
|---|---|---|
| 1 | 1.779 | 57,8% |
| 2 | 411 | 13,4% |
| 3 | 313 | 10,2% |
| 4 | 147 | 4,8% |
| 5 | 76 | 2,5% |
| 6 ke atas | 352 | 11,4% |

**81,4%** mahasiswa mendapat supervisor dari **top-3** rekomendasi. Yang jatuh di rank 6+ bukan kegagalan model — konsekuensi matematis saturasi kapasitas.

### 4.8 Aplikasi Web (9 Halaman)

| Halaman | Fungsi Utama |
|---|---|
| **Login** | Autentikasi EPC (username + password) |
| **Register** | Buat akun baru (username ≥3 char, password ≥6 char) |
| **Dashboard** | Summary stats, Quality Trend chart, shortcut ke detail/export run terakhir |
| **Data Center** | ETL trigger: upload Excel → upsert ke DB (6 kolom wajib: STUDENT ID, TRACK, PARTNER/LECTURER, POSITION/TOPIC, WORK SCHEMA, GPA) |
| **Generate New Run** | Modal konfigurasi pipeline: pilih model, toggle extra\_docs & group\_bonus, set kapasitas min/max |
| **Run History** | Tabel kronologis semua run (desc); klik Detail Row / Export per run |
| **Run Detail** | Metrik evaluasi run, kapasitas per supervisor (warna hijau/merah), mismatch spotlight (10 baris terbesar perbedaannya) |
| **Run Recommendations** | Tabel full penugasan per mahasiswa; filter nama/topik/supervisor; mismatch highlight; Export Standard (3 sheet) / Detailed (4 sheet) |
| **Supervisor Studio** | Kelola profil supervisor: searchable dropdown, Keywords Studio (chip add/remove), Export Config Excel |

### 4.9 Black Box Testing — Equivalence Partitioning

**56 skenario total, semua Pass.**

| Halaman | Skenario |
|---|---|
| Login | 3 |
| Register | 5 |
| Dashboard | 10 |
| Data Center | 7 |
| Generate New Run | 8 |
| Run History | 5 |
| Supervisor Studio | 9 |
| Run Detail | 7 |
| Logout | 2 |
| **Total** | **56 — semua Pass** |

---

## BAB 5 — Simpulan dan Saran

### 5.1 Simpulan (4 poin, jawaban langsung ke RQ)

1. **[RQ1]** Proses manual saat ini: 1–3 minggu/batch, rentan inkonsistensi, tidak ada mekanisme pencocokan keahlian, beban tidak merata.

2. **[RQ1 — sistem]** Sistem dikembangkan dengan scoring hybrid: (a) semantic similarity berbasis text embedding + (b) company group bonus → greedy solver dengan constraint kapasitas. Implementasi: web app 8 halaman fungsional. Black box testing: semua 56 skenario Pass.

3. **[RQ2]** Model terbaik: BAAI/bge-m3 (extra\_docs=True) — MRR 0,711, Hit@5 92,9%, avg\_rank 2,17 (jauh di atas random baseline MRR ~0,23). Assignment Match Rate 53,0% — selaras memadai dengan histori EPC (ground truth sendiri bukan berbasis keahlian murni).

4. **[RQ3]** Sistem berpotensi meningkatkan efisiensi melalui otomasi perhitungan + antarmuka langsung untuk EPC → transparansi dan konsistensi terjaga tanpa menghilangkan otoritas keputusan EPC.

### 5.2 Saran (7 rekomendasi)

1. **Perluasan data supervisor** — tambah publikasi ilmiah, profil penelitian, portofolio proyek.
2. **Perluasan cakupan Enrichment** — jalur research, entrepreneurship, professional development.
3. **Fine-tuning embedding** pada korpus akademik Indonesia / domain Program Enrichment.
4. **Evaluasi usabilitas SUS** dengan EPC nyata sebagai pengguna (saat ini hanya black box fungsional).
5. **Integrasi dengan sistem informasi akademik** XYZ — kurangi manual upload Excel.
6. **Studi longitudinal** — ikuti hasil rekomendasi beberapa periode pemetaan.
7. **Replikasi ke fakultas lain** — konfirmasi generalisabilitas model di luar Computer Science.

---

## Ringkasan Temuan Kunci (untuk hafalan sidang)

### Tiga temuan utama:

**1. BAAI/bge-m3 adalah model terbaik.**  
Hit@1 56%, Hit@5 92,9%, MRR 0,711 — jauh di atas random baseline. Keunggulan konsisten di semua metrik retrieval.

**2. extra\_docs penting tapi model-dependent.**  
Untuk bge-m3: +5,8 pp. Untuk mE5-large: −6,4 pp. Tidak ada konfigurasi tunggal optimal untuk semua model.

**3. Greedy solver menyelesaikan masalah distribusi beban sepenuhnya.**  
Gini 0,0138, selisih maksimum hanya 1 mahasiswa antar supervisor (12–13). Independen dari pilihan model embedding.

### Satu temuan negatif yang jujur:
**group\_bonus tidak mengubah ranking secara terukur** (selisih 0 pp pada ketiga model ketika extra\_docs dikontrol). Fitur aktif secara teknis tapi tidak berdampak pada kualitas ranking.

### Anomali mE5 — cara menjelaskan:
mE5-large-instruct punya Match Rate tertinggi (58,3%) tapi retrieval lebih rendah dari bge-m3. Sebabnya: sebaran skor hanya 0,014 (hampir seragam) — model ini menilai semua pasangan "sangat mirip" sehingga ranking ditentukan slot kapasitas, bukan semantik. Ini bukan kualitas rekomendasi yang lebih baik — ini artefak kalibrasi skor.

---

## Potensi Pertanyaan Sidang dan Jawaban Singkat

**Q: Kenapa tidak fine-tune model embedding ke domain Indonesia?**  
A: Zero-shot multilingual embedding sudah mencapai MRR 0,711 dan Hit@5 92,9% tanpa fine-tuning. Fine-tuning memerlukan dataset berlabel yang tidak tersedia. Ini masuk saran pengembangan (BAB 5, saran 3).

**Q: Kenapa ground truth dari EPC yang manual, bukan yang "benar" secara akademik?**  
A: Ground truth = data institusional penugasan aktual (`current_supervisor_code`) yang telah diterima dan diterapkan EPC. Ini satu-satunya referensi yang tersedia secara operasional. Keterbatasan ini diakui di BAB 1 — sistem adalah DSS, bukan pengganti keputusan manusia.

**Q: Match Rate 53% — apakah itu cukup baik?**  
A: Ya, dalam konteks ini. Ground truth sendiri merupakan hasil pemetaan manual yang tidak selalu berbasis keahlian (BAB 1: penentuan "acak"). Match rate >50% pada kondisi tersebut bermakna. Metrik retrieval (MRR 0,711, Hit@5 92,9%) lebih representatif terhadap kualitas rekomendasi.

**Q: Apakah sistem bisa digunakan untuk selain jalur magang?**  
A: Saat ini tidak — scope dibatasi pada jalur internship di BAB 1. Perluasan ke research/entrepreneurship masuk saran BAB 5.

**Q: Kenapa SQLite bukan PostgreSQL atau vector database?**  
A: Similarity dihitung on-the-fly via NumPy matrix ops (bukan persistent vector store). SQLite cukup untuk prototipe single-process. Skalabilitas ke PostgreSQL bisa dilakukan — SQLAlchemy sudah mendukung multi-backend.

**Q: Apakah 168 sampel cukup untuk evaluasi?**  
A: Ini seluruh populasi batch 2026 CS XYZ (171, dikurangi 3 Study Abroad tanpa supervisor CS). Evaluasi exhaustive (bukan sampling), bukan probabilistik — tidak perlu uji signifikansi statistik untuk populasi penuh.

**Q: Apa perbedaan content-based score vs hybrid score?**  
A: Content-based = cosine similarity murni. Hybrid = content-based + company group bonus. Dalam eksperimen ini, hybrid ≈ content-based karena group bonus terlalu kecil (~0,008) untuk menggeser urutan ranking.

**Q: Kenapa Qwen3-0.6B kalah dibandingkan bge-m3 padahal context window-nya lebih besar?**  
A: Panjang context tidak selalu berarti representasi lebih baik. Dokumen supervisor tidak panjang sehingga keunggulan 32K token tidak relevan. bge-m3 unggul karena arsitektur multi-retrieval yang lebih diskriminatif pada dense retrieval.

---

## Status Dokumen per 2026-07-29

| Bab | Status | Baris |
|---|---|---|
| BAB 1 | Selesai | ~161 |
| BAB 2 | Selesai | ~492 |
| BAB 3 | Selesai | ~2326 |
| BAB 4 | Selesai | 659 |
| BAB 5 | Selesai | 40 |
| Abstrak (ID+EN) | Selesai | 61 |

Semua bab selesai dan terisi konten nyata (tidak ada lipsum).
