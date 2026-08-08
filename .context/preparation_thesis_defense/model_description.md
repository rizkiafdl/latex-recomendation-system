# Model Description — Embedding Models yang Digunakan/Dibandingkan

**Dibuat:** 2026-07-29  
Referensi paper: `reference/bge-m3-embedding.md`, `reference/multilingual-e5.md`, `reference/qwen3-embedding.md`

---

## Overview Perbandingan Cepat

| Aspek | BGE-M3 | Multilingual E5 (mE5) | Qwen3 Embedding |
|---|---|---|---|
| Organisasi | BAAI + USTC | Microsoft | Tongyi Lab, Alibaba |
| Tahun | 2024 (ACL) | 2024 (arXiv) | 2025 (arXiv) |
| Backbone | XLM-RoBERTa (encoder-only) | XLM-RoBERTa / MiniLM (encoder-only) | Qwen3 LLM (decoder-only) |
| Tipe transformer | BERT-like | BERT-like | GPT-like |
| Dimensi vektor | **1024** | 384 / 768 / **1024** | 1024 / 2560 / **4096** |
| Max token | 8,192 | 512 | ~32k+ |
| Yang dipakai di skripsi | ✅ **Run 26 (model utama)** | ❌ (baseline perbandingan) | ❌ (terlalu baru, belum diuji) |

---

## 1. BGE-M3 (M3-Embedding)

### Identitas
- **Nama lengkap:** M3-Embedding: Multi-Linguality, Multi-Functionality, Multi-Granularity
- **Penulis:** Jianlv Chen, Shitao Xiao, Peitian Zhang, Kun Luo, Defu Lian, Zheng Liu (BAAI + USTC)
- **Publikasi:** Findings of ACL 2024
- **Kode/Model:** https://github.com/FlagOpen/FlagEmbedding

### Arsitektur

**Tipe: Encoder-only Transformer → BERT-like**

BGE-M3 dibangun di atas **XLM-RoBERTa** yang kemudian di-pre-train ulang menggunakan metode **RetroMAE** (Masked Auto-Encoder untuk retrieval). XLM-RoBERTa sendiri adalah evolusi dari RoBERTa (versi BERT yang dioptimasi), sehingga arsitekturnya identik dengan BERT:

- Hanya menggunakan encoder (bukan encoder-decoder)
- Setiap token diproses secara bidireksional (kiri dan kanan sekaligus)
- Token `[CLS]` → dipakai sebagai representasi dense (vektor embedding dokumen)
- Token lain → dipakai untuk sparse retrieval dan multi-vector retrieval

**Inovasi utama — 3 mode retrieval dalam 1 model:**

| Mode | Cara Hitung Relevansi |
|---|---|
| **Dense** | Inner product antara embedding `[CLS]` query dan dokumen |
| **Sparse (Lexical)** | Bobot tiap token → mirip BM25 tapi learned |
| **Multi-vector** | Late-interaction ColBERT-style antara seluruh token |
| **Hybrid (All)** | Kombinasi weighted dari ketiga skor di atas |

**Self-Knowledge Distillation:** Skor gabungan ketiga mode dijadikan "teacher signal" untuk melatih masing-masing mode, menghindari konflik antar objective.

**Ukuran model:** ~0.6B parameter (XLM-RoBERTa large backbone)  
**Dimensi embedding:** **1024**  
**Max sequence length:** 8,192 token

### Data Training

Training dilakukan 3 tahap dengan 3 sumber data berbeda:

**Tahap 1 — Pre-training Unsupervised (1.2 miliar pasang teks)**

| Sumber | Contoh Pasangan |
|---|---|
| Wikipedia | (judul seksi, isi seksi) |
| S2ORC | (judul paper, abstrak), citation pairs |
| xP3 | (instruction, output) |
| mC4 | (judul, konten halaman) |
| CC-News | (judul, konten berita) |
| NLLB + CCMatrix | Translation pairs (cross-lingual) |
| MTP (BGE curated) | Beragam pasangan teks Cina |

Total: 1.2 miliar pasang teks, **194 bahasa**, 2655 korespondensi cross-lingual.

**Tahap 2 — Fine-tuning Supervised (high-quality labeled data)**

- *English:* HotpotQA, TriviaQA, NQ, MS MARCO, COLIEE, PubMedQA, SQuAD, SimCSE NLI
- *Chinese:* DuReader, mMARCO-ZH, T²-Ranking, LawGPT, CMedQAv2, NLIzh, LeCaRDv2
- *Multilingual:* Mr. Tydi, MIRACL

**Tahap 3 — Synthetic Data (long document retrieval)**

GPT-3.5 digunakan untuk membuat pasangan (pertanyaan, artikel panjang) dari Wikipedia, Wudao, dan mC4, untuk melatih kemampuan long-document retrieval.

### Evaluasi dari Paper

**Multi-lingual retrieval — MIRACL (nDCG@10):**

| Method | Avg (18 bahasa) |
|---|---|
| BM25 | 31.9 |
| mE5-large | 66.6 |
| E5mistral-7b | 63.4 |
| **M3 Dense** | **69.2** |
| **M3 All (hybrid)** | **71.5** |

**Cross-lingual retrieval — MKQA (Recall@100):**

| Method | Avg (25 bahasa) |
|---|---|
| mE5-large | 70.9 |
| **M3 All** | **75.5** |

**Long-document retrieval — MLDR (nDCG@10):**

| Method | Avg (13 bahasa) |
|---|---|
| mE5-large (512 token) | 34.2 |
| **M3 All (8192 token)** | **65.0** |

**Kesimpulan evaluasi:** BGE-M3 unggul di semua tipe retrieval karena kemampuan hybrid (dense + sparse + multi-vector) dan dukungan dokumen panjang (8192 token).

---

## 2. Multilingual E5 (mE5)

### Identitas
- **Nama lengkap:** Multilingual E5 Text Embeddings: A Technical Report
- **Penulis:** Wang et al. (Microsoft)
- **Publikasi:** arXiv 2402.05672, 2024
- **Kode/Model:** https://github.com/microsoft/unilm/tree/master/e5

### Arsitektur

**Tipe: Encoder-only Transformer → BERT-like**

mE5 menggunakan backbone **XLM-RoBERTa** (varian base dan large) serta **multilingual MiniLM** (untuk varian small). Ketiganya adalah model encoder-only:

| Varian | Backbone | Params | Dimensi |
|---|---|---|---|
| mE5-small | multilingual MiniLM | ~22M | 384 |
| mE5-base | XLM-RoBERTa-base | ~278M | 768 |
| mE5-large | XLM-RoBERTa-large | ~560M | **1024** |
| mE5-large-instruct | XLM-RoBERTa-large + instruction tuning | ~560M | **1024** |

**Perbedaan mE5-large vs mE5-large-instruct:**
- mE5-large: dilatih standard dua tahap (pre-train + fine-tune)
- mE5-large-instruct: ditambah 500k synthetic data dari GPT-3.5/4 dengan 150k unique instructions dalam 93 bahasa → model mengerti "tugas" embedding via natural language instruction

**Yang digunakan di skripsi:** mE5-large-instruct (Run sebelum Run 26, sebagai baseline perbandingan)

**Cara embedding:** Mean pooling atau [CLS] token dari encoder output  
**Max sequence length:** 512 token (keterbatasan XLM-RoBERTa)

### Data Training

Training dua tahap, mengikuti resep E5 bahasa Inggris:

**Tahap 1 — Contrastive Pre-training (~1 miliar pasang teks)**

| Sumber Data | Pasangan |
|---|---|
| Wikipedia | (judul seksi, isi seksi) |
| mC4 | (judul, konten halaman) |
| Multilingual CC-News | (judul, konten berita) |
| NLLB | Translation pairs |
| Reddit | (komentar, respons) |
| S2ORC | (judul, abstrak), citation pairs |
| Stackexchange | (pertanyaan, jawaban) |
| xP3 | (prompt, output) |
| SBERT misc | SimpleWiki, WikiAnswers, AGNews, AmazonQA, AmazonReview, CNN/DailyMail, WikiHow, XSum, dll. |

**Tahap 2 — Supervised Fine-tuning (high-quality labeled)**

ELI5, HotpotQA, FEVER, MIRACL, MS MARCO (passage + doc), NQ, NLLB (100k), NLI (SimCSE), SQuAD, TriviaQA, Quora Duplicate Questions, MrTyDi, DuReader

**mE5-large-instruct tambahan:** 500k synthetic data dari GPT-3.5/4, mencakup 150k unique task instructions dan 93 bahasa.

### Evaluasi dari Paper

**English MTEB Benchmark (56 datasets):**

| Model | MTEB Score |
|---|---|
| BGE-large-en-v1.5 (English only) | baseline |
| Cohere-multilingual-v3 | prev. SOTA multilingual |
| **mE5-large-instruct** | **+0.4 vs Cohere, +0.2 vs BGE-large** |

**Multilingual Retrieval — MIRACL (avg 16 bahasa, nDCG@10):**

| Model | nDCG@10 | R@100 |
|---|---|---|
| mDPR (fine-tuned on MIRACL) | lebih rendah | lebih rendah |
| **mE5 large** | signifikan lebih tinggi | signifikan lebih tinggi |
| **mE5 large-instruct** | tertinggi di kelas ini | tertinggi |

**Bitext Mining (>100 bahasa):** mE5-large-instruct melampaui LaBSE (model yang memang dirancang khusus untuk bitext mining) berkat synthetic data instruction-tuning.

---

## 3. Qwen3 Embedding

### Identitas
- **Nama lengkap:** Qwen3 Embedding: Advancing Text Embedding and Reranking Through Foundation Models
- **Penulis:** Yanzhao Zhang, Mingxin Li, Dingkun Long, et al. (Tongyi Lab, Alibaba Group)
- **Publikasi:** arXiv 2506.05176, 2025
- **Kode/Model:** https://github.com/QwenLM/Qwen3-Embedding

### Arsitektur

**Tipe: Decoder-only Transformer → GPT-like**

Ini adalah perbedaan fundamental dari BGE-M3 dan mE5. Qwen3 Embedding dibangun di atas **Qwen3 foundation models** yang merupakan Large Language Model (LLM) dengan arsitektur **decoder-only**:

| Aspek | BGE-M3 / mE5 (BERT-like) | Qwen3 Embedding (GPT-like) |
|---|---|---|
| Jenis transformer | Encoder-only | Decoder-only |
| Arah attention | Bidireksional (kiri + kanan) | Unidireksional (kiri ke kanan saja) |
| Pre-training objective | Masked Language Modeling (MLM) | Next-token prediction (causal LM) |
| Token untuk embedding | `[CLS]` di awal | Token terakhir / `[EOS]` di akhir |
| Kekuatan | Representasi konteks penuh | World knowledge lebih kaya, instruction-following |

**Tiga ukuran model tersedia:**

| Model | Params | Dimensi Embedding |
|---|---|---|
| Qwen3-Embedding-0.6B | 0.6B | **1024** |
| Qwen3-Embedding-4B | 4B | **2560** |
| Qwen3-Embedding-8B | 8B | **4096** |

**Fitur khusus:**
- **Flexible dimension:** Dimensi output bisa dikurangi (matryoshka-style) tanpa melatih ulang model
- **Customizable instructions:** Mendukung instruction per-query maupun per-dokumen
- **Model merging:** Setelah fine-tuning, checkpoint di-merge via spherical linear interpolation (slerp) untuk meningkatkan robustness

### Data Training

Multi-stage training dengan inovasi utama di **synthetic data**:

**Tahap 1 — Large-Scale Synthetic Weak Supervision (~150 juta pasang)**

Alih-alih mengumpulkan data dari internet (seperti BGE-M3 dan mE5), Qwen3 menggunakan **Qwen3-32B** untuk men-*synthesize* data training:

- Task beragam: retrieval, bitext mining, classification, STS
- Multi-bahasa, termasuk low-resource languages
- Prompting multi-dimensi: tipe query (keyword/faktual/summary/judgment), panjang query, tingkat kesulitan, bahasa target
- Untuk setiap dokumen: model memilih 5 "role" kandidat dari role library untuk mensimulasikan perspektif pengguna yang berbeda → query lebih beragam dan realistis

**Tahap 2 — Supervised Fine-tuning (~12 juta pasang high-quality)**

Filter dari synthetic data tahap 1 menggunakan cosine similarity > 0.7. Data berkualitas tinggi (karena dihasilkan Qwen3-32B) sehingga langsung layak untuk supervised training.

**Tahap 3 — Model Merging**

Checkpoint terbaik dari fine-tuning di-merge via slerp → meningkatkan generalisasi lintas distribusi data.

**Catatan penting:** Tidak ada open-source Q&A forum atau Wikipedia scraping → seluruhnya synthetic dari LLM. Ini berbeda dari pendekatan BGE-M3 dan mE5.

### Evaluasi dari Paper

**MTEB Multilingual (131 tasks, 250+ bahasa):**

| Model | Params | Mean (Task) |
|---|---|---|
| BGE-M3 | 0.6B | 59.56 |
| mE5-large-instruct | 0.6B | 63.22 |
| Gemini Embedding (Google) | — | 68.37 |
| **Qwen3-Embedding-0.6B** | 0.6B | **64.33** |
| **Qwen3-Embedding-4B** | 4B | **69.45** |
| **Qwen3-Embedding-8B** | 8B | **70.58** ← SOTA |

**MTEB English v2 (41 tasks):**

| Model | Dim | Score |
|---|---|---|
| mE5-large-instruct | 1024 | 65.53 |
| Gemini Embedding | 3072 | 73.30 |
| **Qwen3-Embedding-8B** | 4096 | **75.22** ← SOTA |

**MTEB Code (12 code retrieval tasks):**

| Model | Score |
|---|---|
| Gemini Embedding | 74.66 |
| **Qwen3-Embedding-8B** | **80.68** ← SOTA |

**Ablation (Qwen3-Embedding-0.6B):**

| Konfigurasi | MMTEB Score |
|---|---|
| Synthetic data only (tanpa SFT) | 58.49 |
| Tanpa synthetic pre-training | 61.21 |
| Tanpa model merging | 62.56 |
| **Full (synthetic + SFT + merge)** | **64.33** |

---

## Ringkasan: Kenapa BGE-M3 Dipilih untuk Skripsi?

| Kriteria | BGE-M3 | mE5-large | Qwen3-8B |
|---|---|---|---|
| Tersedia on-premises (tanpa GPU besar) | ✅ 0.6B | ✅ 0.6B | ❌ 8B berat |
| Dukungan Bahasa Indonesia | ✅ 194 bahasa | ✅ 100+ bahasa | ✅ 100+ bahasa |
| Konteks panjang (profil mahasiswa panjang) | ✅ 8192 token | ❌ 512 token | ✅ 32k+ |
| Retrieval mode hybrid | ✅ Dense+Sparse+Multi-vec | ❌ Dense only | ❌ Dense only (embedding model) |
| Dimensi vektor | 1024 | 1024 | 1024–4096 |
| SOTA saat eksperimen dilakukan | ✅ (2024) | sebelumnya | ✅ (2025, lebih baru) |
| Publikasi peer-reviewed | ✅ ACL 2024 | arXiv | arXiv |

**Kesimpulan:** BGE-M3 dipilih karena kombinasi unik hybrid retrieval + long-context (8192 token) + multilingual + ukuran model yang feasible untuk on-premises deployment. Qwen3 lebih baik secara benchmark namun dirilis 2025 (setelah eksperimen selesai) dan memerlukan GPU lebih besar untuk model 4B/8B.

---

## Catatan Teknis: BERT-like vs GPT-like untuk Embedding

**BERT-like (Encoder-only):**
- Dilatih dengan Masked Language Modeling (MLM): sebagian token disembunyikan, model prediksi token tersebut
- Attention bersifat bidireksional → setiap token "melihat" semua token lain
- Token `[CLS]` di awal kalimat mengakumulasi representasi seluruh kalimat → dipakai sebagai vektor embedding
- Cocok untuk: text classification, sentence similarity, retrieval (dense/sparse)
- Contoh: BERT, RoBERTa, XLM-RoBERTa, DistilBERT

**GPT-like (Decoder-only):**
- Dilatih dengan Next-Token Prediction (Causal LM): prediksi token berikutnya
- Attention bersifat unidireksional (causal masking) → token hanya "melihat" token sebelumnya
- Token terakhir (`[EOS]`) mengakumulasi representasi seluruh konteks yang telah dibaca → dipakai sebagai vektor embedding
- Cocok untuk: generasi teks, instruksi kompleks, world-knowledge retrieval
- Contoh: GPT-2, GPT-3, Llama, Qwen, Mistral
- Untuk dijadikan embedding model → perlu fine-tuning khusus (kontrastif) karena secara default tidak didesain untuk representasi

**Trade-off:**
- BERT-like lebih efisien untuk retrieval (lebih kecil, lebih cepat)
- GPT-like punya world knowledge lebih kaya (hasil pre-training causal LM yang besar) → embedding lebih kaya secara semantik, tapi lebih lambat dan lebih besar
