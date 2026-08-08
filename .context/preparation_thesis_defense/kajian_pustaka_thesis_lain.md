# Kajian Pustaka — Thesis Lain (Sistem Rekomendasi Akademik)

**Dibuat:** 2026-07-29

---

## Overview Cepat

| Aspek | AVRD (Wang et al., 2025) | BERT Hybrid (Sangeetha et al., 2025) | SPECTER (Cohan et al., 2020) |
|---|---|---|---|
| Problem | Rekomendasi dosen pembimbing | Rekomendasi profil kolaborasi akademik | Rekomendasi paper ilmiah |
| Data | Profil dosen + kuesioner mahasiswa | Survey profil mahasiswa/dosen + synthetic | Paper Semantic Scholar + citation graph |
| Metode utama | Chinese BERT + SimCSE + time decay | TF-IDF + BERT (Hybrid) | SciBERT + triplet loss dari citation |
| Metrik | MAP, HR, I-REA, II-REA | NDCG, MAP, Silhouette | MAP, nDCG, F1 |
| Publikasi | Frontiers in Education | arXiv (VIT Chennai) | ACL 2020 (Allen AI) |

---

## 1. AVRD — Advisor Recommendation (Wang et al., 2025)

**Frontiers in Education 10:1673956 | DOI: 10.3389/feduc.2025.1673956**

### Data
- **Sumber:** Sistem informasi China University of Petroleum
- **Data dosen:** 30,458 record teks → 3 tipe:
  - Course records: 68,792 entri (nama mata kuliah + tahun ajar)
  - Research direction records: 1,970 entri (bidang penelitian dosen)
  - Paper publication records: 28,754 judul paper (1999–2021)
  - Total kandidat dosen aktif: **1,921 dosen**
- **Data mahasiswa:** Kuesioner dari **170 mahasiswa**, berisi teks permintaan singkat (nama mata kuliah, bidang, kata kunci proyek) + daftar dosen ideal sebagai ground truth

### Metode
1. **Embedding teks:** Chinese BERT (RoBERTa-wwm-ext) + unsupervised SimCSE → vektor 768 dimensi per record
2. **Representasi dosen:** Weighted average dari seluruh record menggunakan **time decay factor η=0.88** (publikasi lama diberi bobot lebih kecil)
3. **Representasi mahasiswa:** Tiga tipe teks query → 3 vektor 768-dim
4. **Rekomendasi:**
   - *Pooling criterion:* Ambil max similarity antara tiap tipe query mahasiswa vs tiga tipe vektor dosen → daftar top-n
   - *Matching criterion:* Cek apakah kata kunci mahasiswa ada di teks dosen → suplemen

### Hasil

| Metode | I-REA@10 | II-REA@10 | MAP@10 | HR@10 |
|---|---|---|---|---|
| TF-IDF | 0.6765 | 0.4059 | 0.4438 | 0.4118 |
| Word2Vec | 0.5824 | 0.2647 | 0.2493 | 0.3098 |
| Qwen (+ SimCSE) | 0.4824 | 0.2235 | 0.2178 | 0.2529 |
| **AVRD** | **0.8059** | **0.5412** | **0.5178** | **0.5118** |
| **AVRD @20** | **0.9000** | **0.7529** | **0.5825** | **0.7000** |

- **I-REA:** % mahasiswa yang mendapat ≥1 dosen benar
- **II-REA:** % mahasiswa yang mendapat ≥2 dosen benar
- SimCSE adalah komponen terpenting — tanpa SimCSE I-REA turun dari 0.806 ke 0.412

---

## 2. BERT Hybrid (Sangeetha et al., 2025)

**arXiv 2502.15223 | VIT Chennai**

### Data
- **Sumber:** Survey online + synthetic data
- **Fields per profil:** Profession, Experience, Interest, Collaboration preference, Domain, Skillset
- **Target:** Rekomendasikan 5 profil paling relevan untuk kolaborasi akademik (mirip "swipe" app)
- Dataset kecil, dilengkapi synthetic profiles untuk memperbesar corpus

### Metode
Tiga pendekatan dibandingkan:

| Pendekatan | Cara Kerja |
|---|---|
| **TF-IDF** | Vektorisasi field `domain + skillset`, cosine similarity, Affinity Propagation clustering |
| **BERT** | Fine-tune `bert-large-uncased` dengan MPNet tokenizer, cosine similarity antar embedding |
| **Hybrid** | Gabung TF-IDF vector + BERT embedding → cosine similarity (terbaik) |

Semua dikelompokkan dengan **Affinity Propagation** (jumlah cluster otomatis), lalu top-5 profil direkomendasikan berdasarkan similarity score.

### Hasil

| Metode | NDCG | MAP | Silhouette |
|---|---|---|---|
| TF-IDF | 0.7634 | 0.8112 | 0.3876 |
| BERT | 0.8329 | 0.7858 | 0.1578 |
| **Hybrid** | **0.8587** | **0.8275** | 0.3383 |

- BERT unggul di NDCG (ranking quality) karena memahami semantik
- TF-IDF unggul di MAP (cluster precision) karena separation lebih jelas
- Hybrid dapat keduanya → performa terbaik

---

## 3. SPECTER (Cohan et al., 2020)

**ACL 2020 | Allen Institute for AI**

### Data
- **Sumber:** Semantic Scholar corpus
- **Training:** ~146K query paper dengan citation links → 684K training triplets
  - Triplet = (query paper, paper yang di-cite, paper yang tidak di-cite)
  - "Hard negatives" = paper yang di-cite oleh paper yang di-cite (2-hop) → lebih susah dibedakan
- **Input per paper:** Judul + abstrak saja (tidak butuh full text)
- **Evaluasi:** SCIDOCS benchmark (7 task: klasifikasi, citation prediction, user activity, rekomendasi)

### Metode
1. **Backbone:** SciBERT (BERT yang di-pretrain di teks ilmiah) → encoder-only transformer
2. **Training signal:** Citation graph — paper yang saling cite → embedding lebih dekat
3. **Loss:** Triplet margin loss: `max(d(Q,P+) - d(Q,P-) + margin, 0)`
4. **Embedding:** Token `[CLS]` dari judul+abstrak sebagai representasi dokumen
5. **Inference:** Hanya butuh judul + abstrak, tanpa butuh informasi citation → bisa embed paper baru

### Hasil (SCIDOCS Benchmark)

| Model | Klasifikasi F1 (avg) | User Activity MAP | Citation MAP | Rekomendasi nDCG | **Avg** |
|---|---|---|---|---|---|
| SciBERT (off-shelf) | 80.2 | 49.2 | 48.3 | 52.1 | 59.6 |
| Sent-BERT | 74.8 | 66.5 | 63.5 | 51.6 | 67.5 |
| SGC (graph-based) | 79.8 | 76.5 | **91.6** | 52.7 | 76.9 |
| **SPECTER** | **84.2** | **84.0** | 88.3 | **53.9** | **80.0** |

- SPECTER +3.1 poin di atas baseline terbaik secara rata-rata
- Online A/B test: **+46.5% clickthrough rate** vs sistem produksi existing
- Poin penting: SciBERT "off-the-shelf" (tanpa fine-tune retrieval) sangat buruk → **bukti bahwa BERT saja tidak cukup untuk retrieval**

---

## Perbandingan dengan Skripsi Ini

| Aspek | AVRD | BERT Hybrid | SPECTER | **Skripsi ini** |
|---|---|---|---|---|
| Use case | Mahasiswa → Dosen | Profil → Profil | Paper → Paper | Mahasiswa → Dosen |
| Bahasa data | Chinese | English | English | Indonesian |
| Model embedding | Chinese BERT + SimCSE | BERT-large | SciBERT + citation | **BGE-M3** |
| Dimensi | 768 | 768 | 768 | 1024 |
| Constraint kapasitas | ❌ | ❌ | ❌ | ✅ Greedy solver (cap 12 mhs/dosen) |
| Metrik utama | MAP, HR | NDCG, MAP | MAP, nDCG | **MRR, Hit@K, nDCG@K, Match Rate** |
| Ground truth | Kuesioner (170 mhs) | Survey + synthetic | Clickthrough log | **Dataset historis (168 mhs, 14 dosen)** |

**Perbedaan utama skripsi ini vs AVRD:**
- AVRD tidak memiliki constraint kapasitas (setiap dosen bisa terima berapa saja mahasiswa)
- Skripsi ini menambahkan greedy solver untuk memastikan distribusi kapasitas realistis (max 12 per dosen)
- AVRD menggunakan Chinese BERT domain-spesifik; skripsi menggunakan BGE-M3 multilingual zero-shot (tanpa fine-tuning domain)
