# Sidang Isbat Last DANCE FUCK YOU

## Plan Presentasi

Slide 1-4 Theo 

Slide 5-8 Doli 

Slide 9-10 (ada showcase juga) Rakha

## Harus cari tau justifikasi dari masing-masing decision di bawah :

- precision (decision penulisan dan impementasi) kenapa ga ditampilin
    - Precision@K memang sempat ingin kami implementasikan pada sistem sebagai metrik tambahan untuk keperluan monitoring. Namun pada evaluasi penelitian kami tidak menjadikannya metrik utama karena karakteristik dataset hanya memiliki satu supervisor ground truth untuk setiap mahasiswa. Dalam kondisi tersebut, Precision@K menjadi kurang informatif karena nilainya hanya bergantung pada ada atau tidaknya supervisor benar di Top-K dan tidak mempertimbangkan posisi supervisor tersebut. Oleh karena itu keputusan akhirnya kami memilih menggunakan MRR, Hit@1, Hit@5, dan NDCG@5 yang lebih sesuai untuk mengevaluasi kualitas ranking rekomendasi dan ada diwebsite karena website kami tidak dikembangkan  sebagai implementasi penelitian tetapi juga sebagai alat evaluasi. Oleh karena itu beberapa metrik tambahan seperti Precision@K tetap saya implementasikan agar sistem dapat dikembangkan lebih lanjut jika di masa depan terdapat lebih dari satu supervisor relevan untuk setiap mahasiswa. Namun untuk analisis dan pembahasan pada penelitian ini, saya fokus pada metrik yang paling representatif terhadap tujuan penelitian.
- Kenapa Model yang dipakai itu si 3 transformer based
    - ⁠Kenapa memilih model yang kita pilih, studi literasi terkait model ini yang membuat kita milih.
        
        Justifikasi BGE-M3 vs BERT
        
        Inti jawaban singkat (30 detik):
        
        ▎ "BGE-M3 dan BERT sebenarnya menggunakan arsitektur yang sama — keduanya encoder-only transformer. Perbedaannya bukan di arsitektur, tapi di tujuan pelatihan. BERT dilatih untuk memahami bahasa (MLM), sementara BGE-M3 dilatih khusus untuk retrieval menggunakan contrastive learning pada 1.2 miliar pasang teks. BERT tanpa fine-tuning retrieval menghasilkan embedding yang buruk untuk semantic search — fenomena ini disebut representation degeneration."
        
        Breakdown detail per argumen:
        
        1. BERT bukan model embedding — BGE-M3 adalah
        
        BERT dilatih dengan Masked Language Modeling: prediksi kata yang disembunyikan. Hasilnya bagus untuk NLU (klasifikasi, NER, QA), tapi token [CLS] dari BERT "mentah" menghasilkan vektor yang buruk untuk cosine similarity. Penelitian Sentence-BERT (Reimers 2019) menunjukkan BERT tanpa fine-tuning retrieval bahkan lebih buruk dari rata-rata word embeddings di tugas sentence similarity.
        
        BGE-M3 dilatih dari XLM-RoBERTa (arsitektur identik BERT) plus contrastive training skala besar → embedding [CLS]-nya secara langsung bermakna untuk semantic retrieval.
        
        1. Keterbatasan 512 token BERT vs 8,192 BGE-M3
        ofil dosen di skripsi ini berisi bidang keahlian + riwayat penelg. BERT memotong input di token ke-512, sehingga bagian pentingprofil bisa hilang. BGE-M3 mendukung hingga 8,192 token — seluruh profil dosen dapat direpresentasikan dalam satu vektor.
        2. Dukungan Bahasa Indonesia
        BERT base (bert-base-uncased) hanya untuk Bahasa Inggris. Alternatifnya adalah bert-base-multilingual-cased (mBERT), tapi mBERT dilatih pada 104 bahasa dengan datng tidak seimbang — Bahasa Indonesia mendapat porsi kecil. BGE-Mgan 1.2 miliar pasang teks dan secara eksplisit dirancang untukmultilingual retrieval.
        3. Tidak perlu fine-tuning manual
        Jika menggunakan BERT, kita harus: Menyiapkan labeled pairs (query, dokumen relevan, dokumen tidak
        - Fine-tune dengan contrastive loss Belum tentu performa optimal karena data training terbatas
        E-M3 sudah melalui proses ini pada data masif — tinggal pakai (oata tambahan.
        -
        Analogi untuk penguji:
        ▎ "Ibarat membandingkan bahan mentah vs produk jadi. BERT adalah tepung terigu — bisa jadi kue kalau diolah dengan benar. BGE-M3 adalah kue yang sudah dipanggang dengan resep optimal untuk retrieval. Saya membutuhkan produk jaan, bukan proses fine-tuning tambahan yang memerlukan labeled datayang tidak tersedia."
        
        ---
        
        Jika ditanya lebih lanjut: "Kenapa tidak fine-tune BERT sendiri?"
        
        - Tidak ada ground-truth labeled pairs "query mahasiswa → dosen yauntuk fine-tuning yang bermakna
        - BGE-M3 sudah dilatih pada S2ORC (paper akademik) dan banyak dataset QA akademik → domain sangat relevan dengan pencocokan mahasiswa-dosen
        - Pendekatan zero-shot dengan BGE-M3 sudah menghasilkan MRR 0.711,0.232)
        
- recall kenapa ditulis
    - Untuk recall sebelumnya sempat dipertimbangkan sebagai salah satu metrik evaluasi retrieval. Tapi setelah direview kembali pada karakterisik permasalahan , kami memutuskan untuk menggunakan MRR, Hit@1, Hit@5, dan NDCG@5 karena metrik tersebut lebih mampu mengevaluasi kualitas urutan (ranking) rekomendasi supervisor. Sedangkan Recall@K hanya mengukur apakah ground truth berhasil ditemukan dalam Top-K tanpa memperhatikan posisinya juga Recall@K lebih umum digunakan pada information retrieval ketika terdapat banyak dokumen relevan yang ingin ditemukan. Sedangkan pada penelitian ini setiap mahasiswa hanya memiliki satu supervisor aktual sebagai ground truth. Oleh karena itu metrik yang mempertimbangkan posisi supervisor pada ranking menjadi lebih representatif dibanding Recall@K.
- random baseline perhitungannya dari mana.
    
    Ide Dasarnya
    
    Bayangkan kamu punya 14 dosen pembimbing, lalu sistem kamu asal tebak secara acak — tidak pakai cosine similarity, tidak pakai embedding, murni random. Berapa nilai metriknya rata-rata?
    
    Karena random, setiap dosen punya peluang sama untuk muncul di posisi mana saja. Peluang dosen manapun berada di posisi tertentu = 1/14.
    
    Hit@1 = 1/14 ≈ 0,07
    
    Hit@1 = 1 jika dosen ground truth (GT) muncul di posisi 1.
    
    Dari 14 posisi, hanya 1 yang benar.
    Peluang = 1/14 ≈ 7%
    
    Artinya: kalau sistem asal tebak, hanya 7% mahasiswa yang dosennya langsung tepat di posisi pertama.
    
    Hit@5 = 5/14 ≈ 0,36
    
    Hit@5 = 1 jika dosen GT muncul di posisi 1, 2, 3, 4, atau 5.
    
    Ada 5 posisi yang dianggap "berhasil" dari total 14.
    Peluang = 5/14 ≈ 36%
    
    Artinya: sekitar 1 dari 3 mahasiswa dosennya kebetulan masuk top-5 meski sistemnya asal tebak.
    
    MRR = H₁₄ / 14 ≈ 0,23
    
    MRR = rata-rata dari 1/rank. Kalau rank acak, kita hitung nilai harapan:
    
    E[1/rank] = peluang rank 1 × (1/1)
    + peluang rank 2 × (1/2)
    + peluang rank 3 × (1/3)
    + ...
    + peluang rank 14 × (1/14)
    
    Karena semua peluang = 1/14:
    
    = (1/14) × (1/1 + 1/2 + 1/3 + ... + 1/14)
    = (1/14) × 3,252
    ≈ 0,232
    
    nDCG@5 ≈ 0,21
    
    nDCG@5 memberi nilai berbeda tergantung posisinya:
    
    Posisi 1 → nilainya 1,000
    Posisi 2 → nilainya 0,631
    Posisi 3 → nilainya 0,500
    Posisi 4 → nilainya 0,431
    Posisi 5 → nilainya 0,387
    Posisi 6–14 → nilainya 0 (di luar jendela @5)
    
    Nilai harapannya:
    
    = (1/14) × (1,000 + 0,631 + 0,500 + 0,431 + 0,387)
    = (1/14) × 2,949
    ≈ 0,211
    
    Avg Rank = 7,5
    
    Kalau ada 14 posisi dan semua sama kemungkinannya, rata-rata posisinya ya tengah-tengah:
    
    (1 + 14) / 2 = 7,5
    
    Ringkasan Logika
    
    Semua rumus di atas pakai prinsip yang sama: nilai harapan distribusi seragam. Karena acak → semua posisi sama peluangnya (1/14) → tinggal kalikan nilai tiap posisi dengan 1/14 lalu jumlahkan.
    
- Kenapa ga ada confidence score dari masing-masing model?
    - **Pergeseran Fokus: Akurasi Kelompok vs. Kepastian Individu**
    - **Justifikasinya:** Tujuan utama sistem Anda adalah menyajikan daftar pilihan terbaik yang relevan bagi pengguna, bukan memprediksi seberapa pasti model terhadap satu item spesifik.
    - **Argumen:** nDCG dan Hit@K mengukur **pengalaman langsung pengguna (*User Experience*)**. Pengguna tidak peduli apakah sistem 85% atau 95% yakin; mereka hanya peduli apakah item yang mereka cari ada di posisi atas halaman (diukur oleh nDCG) atau setidaknya muncul di layar (diukur oleh Hit@K).**2. Peringkat Relatif Lebih Penting daripada Probabilitas Absolut**
    - **Justifikasinya:** Untuk merekomendasikan sesuatu, Anda hanya butuh urutan urutan (Item A > Item B > Item C). Anda tidak butuh nilai kepastian absolut.
    - **Argumen:** Model *learning-to-rank* modern dioptimalkan langsung menggunakan fungsi kerugian (*loss function*) berbasis peringkat (seperti *Pairwise* atau *Listwise loss*). Model ini dirancang untuk menghasilkan urutan yang benar, sehingga skor mentah internalnya sering kali tidak terkalibrasi sebagai probabilitas nyata dan tidak perlu dipakai sebagai *confidence score*.**3. Justifikasi Berdasarkan Karakteristik Metrik Anda**
    - **Hit@K (Efektivitas Tampilan):** Menjustifikasi bahwa sistem berhasil menjamin item yang relevan masuk ke dalam slot ruang layar yang terbatas (misal: Top-5 atau Top-10 di aplikasi *mobile*).
    - **nDCG (Kualitas Urutan):** Menjustifikasi bahwa sistem tidak hanya asal menebak, tetapi berhasil menaruh item yang paling relevan di posisi paling atas demi mengurangi beban gulir (*scrolling effort*) pengguna.

## Todo In General July 28, 2026

- Tabel perbandingan model kita ataupun penelitian kita dengan penelitian yang sudah ada
- Jelasin detail UML dan juga relationnya
- Kenapa ada 5 rekomendasi dan yang dishow hanya 1
- ⁠Lengkapi referensi dari setiap decision yang diambil itu karena apa

## Landasan Theory

Belajar sendiri aja 

Count Vec : 

- Apa itu tf-idf
    - TF-IDF measures how important a word is to a document within a collection, by combining how often it appears in that document with how rare it is across all documents.

Neural Network : 

- Apa itu word 2 vec

Transformer Based : 

- dengan transformer (harus tau transformer itu apa)
- kenapa kita tidak pakai bert (harus tau bert dulu apa)
- Karena bert itu built untuk produce token sedangkan untuk embeding model ini sentece transformer memang built in

Studi kasus rekomendasi yang di tinjau itu pakai bert, tfidf, something kmeans. Ini gimana hasilnya dan perbedaan nya dengan barang punya kita apa.

- Project yang mirip sama kita :
    
    ## Overview Cepat
    
    | Aspek | AVRD (Wang et al., 2025) | BERT Hybrid (Sangeetha et al., 2025) | SPECTER (Cohan et al., 2020) |
    | --- | --- | --- | --- |
    | Problem | Rekomendasi dosen pembimbing | Rekomendasi profil kolaborasi akademik | Rekomendasi paper ilmiah |
    | Data | Profil dosen + kuesioner mahasiswa | Survey profil mahasiswa/dosen + synthetic | Paper Semantic Scholar + citation graph |
    | Metode utama | Chinese BERT + SimCSE + time decay | TF-IDF + BERT (Hybrid) | SciBERT + triplet loss dari citation |
    | Metrik | MAP, HR, I-REA, II-REA | NDCG, MAP, Silhouette | MAP, nDCG, F1 |
    | Publikasi | Frontiers in Education | arXiv (VIT Chennai) | ACL 2020 (Allen AI) |
    
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
    | --- | --- | --- | --- | --- |
    | TF-IDF | 0.6765 | 0.4059 | 0.4438 | 0.4118 |
    | Word2Vec | 0.5824 | 0.2647 | 0.2493 | 0.3098 |
    | Qwen (+ SimCSE) | 0.4824 | 0.2235 | 0.2178 | 0.2529 |
    | **AVRD** | **0.8059** | **0.5412** | **0.5178** | **0.5118** |
    | **AVRD @20** | **0.9000** | **0.7529** | **0.5825** | **0.7000** |
    - **I-REA:** % mahasiswa yang mendapat ≥1 dosen benar
    - **II-REA:** % mahasiswa yang mendapat ≥2 dosen benar
    - SimCSE adalah komponen terpenting — tanpa SimCSE I-REA turun dari 0.806 ke 0.412
    
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
    | --- | --- |
    | **TF-IDF** | Vektorisasi field `domain + skillset`, cosine similarity, Affinity Propagation clustering |
    | **BERT** | Fine-tune `bert-large-uncased` dengan MPNet tokenizer, cosine similarity antar embedding |
    | **Hybrid** | Gabung TF-IDF vector + BERT embedding → cosine similarity (terbaik) |
    
    Semua dikelompokkan dengan **Affinity Propagation** (jumlah cluster otomatis), lalu top-5 profil direkomendasikan berdasarkan similarity score.
    
    ### Hasil
    
    | Metode | NDCG | MAP | Silhouette |
    | --- | --- | --- | --- |
    | TF-IDF | 0.7634 | 0.8112 | 0.3876 |
    | BERT | 0.8329 | 0.7858 | 0.1578 |
    | **Hybrid** | **0.8587** | **0.8275** | 0.3383 |
    - BERT unggul di NDCG (ranking quality) karena memahami semantik
    - TF-IDF unggul di MAP (cluster precision) karena separation lebih jelas
    - Hybrid dapat keduanya → performa terbaik
    
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
    | --- | --- | --- | --- | --- | --- |
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
    | --- | --- | --- | --- | --- |
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
- SPECTER: Document - level Rep- resentation Learning using Citation - informed Transformers nama model (BERT & Ci- tationGraph (SPECTER))
- A BERT Based Hybrid Recommen- dation System For Academic Collaboration
- Empowering College Students to Select Ideal Advisors: A Text-Based Recommen- dation Model

### masing-masing karakteristik model yang kita pakai

> Link PDF : 
[https://arxiv.org/abs/2506.05176](https://arxiv.org/abs/2506.05176) QWEN 
[https://arxiv.org/abs/2402.05672](https://arxiv.org/abs/2402.05672) E5 
[https://aclanthology.org/2024.findings-acl.137/](https://aclanthology.org/2024.findings-acl.137/) BAAI/M3 (best Model)
> 

Referensi paper: `reference/bge-m3-embedding.md`, `reference/multilingual-e5.md`, `reference/qwen3-embedding.md`

---

## Overview Perbandingan Cepat

| Aspek | BGE-M3 | Multilingual E5 (mE5) | Qwen3 Embedding |
| --- | --- | --- | --- |
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
- **Kode/Model:** [https://github.com/FlagOpen/FlagEmbedding](https://github.com/FlagOpen/FlagEmbedding)

### Arsitektur

**Tipe: Encoder-only Transformer → BERT-like**

BGE-M3 dibangun di atas **XLM-RoBERTa** yang kemudian di-pre-train ulang menggunakan metode **RetroMAE** (Masked Auto-Encoder untuk retrieval). XLM-RoBERTa sendiri adalah evolusi dari RoBERTa (versi BERT yang dioptimasi), sehingga arsitekturnya identik dengan BERT:

- Hanya menggunakan encoder (bukan encoder-decoder)
- Setiap token diproses secara bidireksional (kiri dan kanan sekaligus)
- Token `[CLS]` → dipakai sebagai representasi dense (vektor embedding dokumen)
- Token lain → dipakai untuk sparse retrieval dan multi-vector retrieval

**Inovasi utama — 3 mode retrieval dalam 1 model:**

| Mode | Cara Hitung Relevansi |
| --- | --- |
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
| --- | --- |
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
| --- | --- |
| BM25 | 31.9 |
| mE5-large | 66.6 |
| E5mistral-7b | 63.4 |
| **M3 Dense** | **69.2** |
| **M3 All (hybrid)** | **71.5** |

**Cross-lingual retrieval — MKQA (Recall@100):**

| Method | Avg (25 bahasa) |
| --- | --- |
| mE5-large | 70.9 |
| **M3 All** | **75.5** |

**Long-document retrieval — MLDR (nDCG@10):**

| Method | Avg (13 bahasa) |
| --- | --- |
| mE5-large (512 token) | 34.2 |
| **M3 All (8192 token)** | **65.0** |

**Kesimpulan evaluasi:** BGE-M3 unggul di semua tipe retrieval karena kemampuan hybrid (dense + sparse + multi-vector) dan dukungan dokumen panjang (8192 token).

## 2. Multilingual E5 (mE5)

### Identitas

- **Nama lengkap:** Multilingual E5 Text Embeddings: A Technical Report
- **Penulis:** Wang et al. (Microsoft)
- **Publikasi:** arXiv 2402.05672, 2024
- **Kode/Model:** [https://github.com/microsoft/unilm/tree/master/e5](https://github.com/microsoft/unilm/tree/master/e5)

### Arsitektur

**Tipe: Encoder-only Transformer → BERT-like**

mE5 menggunakan backbone **XLM-RoBERTa** (varian base dan large) serta **multilingual MiniLM** (untuk varian small). Ketiganya adalah model encoder-only:

| Varian | Backbone | Params | Dimensi |
| --- | --- | --- | --- |
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
| --- | --- |
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
| --- | --- |
| BGE-large-en-v1.5 (English only) | baseline |
| Cohere-multilingual-v3 | prev. SOTA multilingual |
| **mE5-large-instruct** | **+0.4 vs Cohere, +0.2 vs BGE-large** |

**Multilingual Retrieval — MIRACL (avg 16 bahasa, nDCG@10):**

| Model | nDCG@10 | R@100 |
| --- | --- | --- |
| mDPR (fine-tuned on MIRACL) | lebih rendah | lebih rendah |
| **mE5 large** | signifikan lebih tinggi | signifikan lebih tinggi |
| **mE5 large-instruct** | tertinggi di kelas ini | tertinggi |

**Bitext Mining (>100 bahasa):** mE5-large-instruct melampaui LaBSE (model yang memang dirancang khusus untuk bitext mining) berkat synthetic data instruction-tuning.

## 3. Qwen3 Embedding

### Identitas

- **Nama lengkap:** Qwen3 Embedding: Advancing Text Embedding and Reranking Through Foundation Models
- **Penulis:** Yanzhao Zhang, Mingxin Li, Dingkun Long, et al. (Tongyi Lab, Alibaba Group)
- **Publikasi:** arXiv 2506.05176, 2025
- **Kode/Model:** [https://github.com/QwenLM/Qwen3-Embedding](https://github.com/QwenLM/Qwen3-Embedding)

### Arsitektur

**Tipe: Decoder-only Transformer → GPT-like**

Ini adalah perbedaan fundamental dari BGE-M3 dan mE5. Qwen3 Embedding dibangun di atas **Qwen3 foundation models** yang merupakan Large Language Model (LLM) dengan arsitektur **decoder-only**:

| Aspek | BGE-M3 / mE5 (BERT-like) | Qwen3 Embedding (GPT-like) |
| --- | --- | --- |
| Jenis transformer | Encoder-only | Decoder-only |
| Arah attention | Bidireksional (kiri + kanan) | Unidireksional (kiri ke kanan saja) |
| Pre-training objective | Masked Language Modeling (MLM) | Next-token prediction (causal LM) |
| Token untuk embedding | `[CLS]` di awal | Token terakhir / `[EOS]` di akhir |
| Kekuatan | Representasi konteks penuh | World knowledge lebih kaya, instruction-following |

**Tiga ukuran model tersedia:**

| Model | Params | Dimensi Embedding |
| --- | --- | --- |
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
| --- | --- | --- |
| BGE-M3 | 0.6B | 59.56 |
| mE5-large-instruct | 0.6B | 63.22 |
| Gemini Embedding (Google) | — | 68.37 |
| **Qwen3-Embedding-0.6B** | 0.6B | **64.33** |
| **Qwen3-Embedding-4B** | 4B | **69.45** |
| **Qwen3-Embedding-8B** | 8B | **70.58** ← SOTA |

**MTEB English v2 (41 tasks):**

| Model | Dim | Score |
| --- | --- | --- |
| mE5-large-instruct | 1024 | 65.53 |
| Gemini Embedding | 3072 | 73.30 |
| **Qwen3-Embedding-8B** | 4096 | **75.22** ← SOTA |

**MTEB Code (12 code retrieval tasks):**

| Model | Score |
| --- | --- |
| Gemini Embedding | 74.66 |
| **Qwen3-Embedding-8B** | **80.68** ← SOTA |

**Ablation (Qwen3-Embedding-0.6B):**

| Konfigurasi | MMTEB Score |
| --- | --- |
| Synthetic data only (tanpa SFT) | 58.49 |
| Tanpa synthetic pre-training | 61.21 |
| Tanpa model merging | 62.56 |
| **Full (synthetic + SFT + merge)** | **64.33** |

## Ringkasan: Kenapa BGE-M3 Dipilih untuk Skripsi?

| Kriteria | BGE-M3 | mE5-large | Qwen3-8B |
| --- | --- | --- | --- |
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

## Cara Hitung Sim Cos

Contoh real : 

1 [”job Desc”,”job desc 2”] 

Vector item 1[121,1231,12313,1231] 4D

Vector item 2 [121,1231,12313,1231] 4D 

nah kita itu case nya ada 168x1024 D= 1024

Aslinya model kita 1024 dimensi (harus cari tau dulu).

## 1. Alur Keseluruhan

```
Teks mahasiswa + dosen
      ↓ model.encode()
Vektor mentah  (panjang bebas, dimensi bisa negatif)
      ↓ L2 normalize (normalize_embeddings=True)
Unit vector    (panjang = 1, dimensi masih bisa negatif)
      ↓ np.matmul(student_vectors, supervisor_vectors.T)
Score matrix   (range -1 sampai +1) = cosine similarity
```

## 2. L2 Normalization

### Apa itu?

L2 normalize = memaksa **panjang vektor menjadi 1** (unit vector).

Bukan membatasi nilai ke 0–1. Nilai tiap dimensi masih bisa negatif.

### Formula panjang vektor

```
||v|| = √(v1² + v2² + v3² + ... + vn²)
```

### Contoh

```
Vektor asli:      [3.0, -1.5, 4.2, -0.8]
Panjang = √(3² + 1.5² + 4.2² + 0.8²) = √(9 + 2.25 + 17.64 + 0.64) = √29.53 ≈ 5.43

Setelah normalize: [3/5.43, -1.5/5.43, 4.2/5.43, -0.8/5.43]
                 = [0.55,   -0.28,      0.77,     -0.15]

Panjang baru = √(0.55² + 0.28² + 0.77² + 0.15²) = 1.0  ✅
```

### Mengapa perlu normalize?

Tanpa normalisasi, teks panjang menghasilkan vektor dengan magnitude besar → dot product lebih besar bukan karena lebih relevan, tapi karena lebih panjang. Normalisasi menghilangkan **bias panjang dokumen** — yang diukur murni **arah (makna)**, bukan kuantitas teks.

## 3. Cosine Similarity

### Formula umum

```
cosine(A, B) = (A · B) / (||A|| × ||B||)
```

### Setelah L2 normalize (||A|| = ||B|| = 1)

```
cosine(A, B) = (A · B) / (1 × 1) = A · B
```

Dot product langsung = cosine similarity. Tidak perlu pembagian lagi.

### Range nilai

```
+1  → arah persis sama   (makna identik)
 0  → tegak lurus        (tidak ada hubungan)
-1  → arah berlawanan    (makna bertolak belakang)
```

### Contoh dot product 4-dimensi

```
A = [0.6,  0.1,  0.7,  0.2]   (unit vector, panjang = 1)
B = [0.7,  0.2,  0.6,  0.1]   (unit vector, panjang = 1)
C = [-0.1, 0.8,  0.3,  0.5]   (unit vector, panjang = 1)

A · B = (0.6×0.7) + (0.1×0.2) + (0.7×0.6) + (0.2×0.1)
      = 0.42 + 0.02 + 0.42 + 0.02 = 0.88   ← sangat mirip

A · C = (0.6×-0.1) + (0.1×0.8) + (0.7×0.3) + (0.2×0.5)
      = -0.06 + 0.08 + 0.21 + 0.10 = 0.33  ← kurang mirip
```

## 4. Kenapa Hasil Praktis 0–1 (Bukan -1 sampai +1)?

Secara matematis range cosine similarity adalah **-1 sampai +1**.

Nilai negatif muncul jika dua vektor berlawanan arah — artinya makna bertolak belakang total.

Dalam embedding teks natural (profil mahasiswa vs profil dosen), vektor cenderung berada di **kuadran yang sama** di ruang berdimensi tinggi — tidak pernah bermakna "berlawanan total". Hasilnya praktis 0–1, tapi ini **karakteristik data**, bukan batasan matematis.

## 5. Matmul = Semua Pasangan Sekaligus

```python
similarity = np.matmul(student_vectors, supervisor_vectors.T)
# (168, 1024) × (1024, 14) → (168, 14)
```

Setiap sel `[i, j]` = cosine similarity mahasiswa-i vs supervisor-j.

```
             D001   D002   D003  ...  D014
mahasiswa 0: [0.85,  0.72,  0.60, ..., 0.30]
mahasiswa 1: [0.55,  0.48,  0.79, ..., 0.51]
...
mahasiswa 167: [...]
```

168 × 14 = **2.352 pasangan** dihitung dalam satu operasi matrix.

## Flow Capacity Solver

## Konteks Aktual Thesis

```
168 mahasiswa valid, 14 supervisor
target min = 10, max = 12 per supervisor
total max capacity = 14 × 12 = 168  → pas, tidak overflow
total min capacity = 14 × 10 = 140  → tidak underflow
```

Contoh di bawah memakai **6 mahasiswa, 3 supervisor** agar mudah ditelusuri.

## Bagian 1 — Capacity Planning

### Step 1: Inisialisasi Batas

```
min_caps = [2, 2, 2]   # setiap supervisor minimal 2 mahasiswa
max_caps = [2, 2, 2]   # setiap supervisor maksimal 2 mahasiswa
```

### Step 2: Ranking Supervisor Berdasarkan Prioritas

```
priority_codes = ["D001"]

ranked = [D001, D002, D003]
# D001 di depan karena prioritas, sisanya urut index
```

### Step 3: Cek Overflow (mahasiswa > total max)

```
Contoh: 10 mahasiswa, total max = 9 → overflow = 1

Distribusi ke priority supervisor (round-robin):
  D001: max_cap → 3   (dapat overflow slot)
  D002: max_cap → 2   (tetap)
  D003: max_cap → 2   (tetap)

Non-priority supervisor tetap hard-capped.
```

### Step 4: Cek Underflow (mahasiswa < total min)

```
Contoh: 4 mahasiswa, total min = 6 → deficit = 2

Kurangi min_cap dari supervisor prioritas TERENDAH (urutan terbalik):
  D003: min_cap → 1   (dikurangi 1)
  D002: min_cap → 1   (dikurangi 1)
  D001: min_cap → 2   (tidak disentuh, prioritas tertinggi)
```

### Kasus Aktual Thesis

```
overflow  = max(0, 168 - 168) = 0   → tidak ada
underflow = max(0, 140 - 168) = 0   → tidak ada

min_caps = [10, 10, ..., 10]   (14 supervisor)
max_caps = [12, 12, ..., 12]   (14 supervisor)
```

## Bagian 2 — Greedy Solver

Input: score matrix hasil cosine similarity + min_caps + max_caps dari Bagian 1.

### Score Matrix Contoh

```
         D001   D002   D003
M0:    [ 0.90,  0.60,  0.40 ]
M1:    [ 0.85,  0.55,  0.35 ]
M2:    [ 0.82,  0.88,  0.50 ]
M3:    [ 0.80,  0.80,  0.45 ]
M4:    [ 0.40,  0.45,  0.92 ]
M5:    [ 0.35,  0.42,  0.88 ]

min_caps = [2, 2, 2]
max_caps = [2, 2, 2]
```

### Phase 1 — Argmax: Setiap Mahasiswa ke Supervisor Terbaik

```python
assignment = np.argmax(score_matrix, axis=1)
```

Ambil nilai tertinggi per baris:

```
M0: max(0.90, 0.60, 0.40) → D001  ✅
M1: max(0.85, 0.55, 0.35) → D001  ✅
M2: max(0.82, 0.88, 0.50) → D002  ✅  (0.88 tertinggi, bukan D001)
M3: max(0.80, 0.80, 0.45) → D001  ✅  (tie D001=D002, ambil index pertama)
M4: max(0.40, 0.45, 0.92) → D003  ✅
M5: max(0.35, 0.42, 0.88) → D003  ✅

assignment = [D001, D001, D002, D001, D003, D003]
counts     = [  3,    1,    2 ]
```

State setelah Phase 1:

```
D001: M0, M1, M3  → count=3, max=2 → OVERFULL ⚠️
D002: M2          → count=1, min=2 → UNDERFULL ⚠️
D003: M4, M5      → count=2        → OK ✅
```

### Phase 2 — Overflow Fix Loop

D001 kelebihan 1 mahasiswa. Cari mahasiswa yang **paling tidak rugi** dipindah ke D002.

**Formula penalti:**

```
penalti = skor_di_supervisor_sekarang - skor_di_supervisor_tujuan
```

Penalti kecil = mahasiswa tidak banyak kehilangan kualitas jika dipindah.

Penalti = 0 = mahasiswa benar-benar tidak rugi dipindah.

```
Kandidat dari D001 → tujuan D002:

M0: 0.90 - 0.60 = 0.30   (rugi 0.30 jika pindah)
M1: 0.85 - 0.55 = 0.30   (rugi 0.30 jika pindah)
M3: 0.80 - 0.80 = 0.00   ← TERENDAH, tidak rugi sama sekali

→ Pilih M3, pindah dari D001 ke D002
```

State setelah Phase 2:

```
assignment = [D001, D001, D002, D002, D003, D003]
counts     = [  2,    2,    2 ]   ← semua dalam batas ✅
```

### Phase 3 — Underflow Fix Loop

Tidak diperlukan di contoh ini — semua supervisor sudah memenuhi min_cap setelah Phase 2.

Jika ada underfull, logikanya sama: ambil mahasiswa dari supervisor yang masih kelebihan, pilih yang penaltinya paling kecil.

### Final Validation

```python
for j in range(3):
    assert min_caps[j] <= counts[j] <= max_caps[j]
# semua terpenuhi ✅

objective = sum(score_matrix[i, assignment[i]] for i in range(6))
# = 0.90 + 0.85 + 0.88 + 0.80 + 0.92 + 0.88 = 5.23
```

`objective` = total skor penugasan akhir yang berhasil dipertahankan.

## Hasil Final

| Mahasiswa | Supervisor Akhir | Skor | Catatan |
| --- | --- | --- | --- |
| M0 | D001 | 0.90 | Pilihan pertama, langsung |
| M1 | D001 | 0.85 | Pilihan pertama, langsung |
| M2 | D002 | 0.88 | Pilihan pertama (argmax), langsung |
| M3 | D002 | 0.80 | Dipindah dari D001, penalti = 0.00 |
| M4 | D003 | 0.92 | Pilihan pertama, langsung |
| M5 | D003 | 0.88 | Pilihan pertama, langsung |

## Ringkasan Logika

```
Phase 1: Semua mahasiswa → supervisor favorit (argmax)
         Hasilnya: mungkin ada yang overfull / underfull

Phase 2: Kurangi yang kelebihan
         → pindahkan mahasiswa dengan penalti terkecil
         → penalti = selisih skor sekarang vs tujuan

Phase 3: Penuhi yang kekurangan
         → ambil dari supervisor yang masih kelebihan
         → lagi-lagi pilih penalti terkecil

Tujuan: memaksimalkan total skor penugasan
        sambil memenuhi constraint min/max per supervisor
```

## Mengapa Disebut "Greedy"?

Setiap iterasi hanya memilih **satu langkah terbaik saat itu** (penalti terkecil), tanpa melihat dampak ke iterasi berikutnya. Ini berbeda dari optimal global (brute-force semua kombinasi).

Trade-off:

- Brute-force: hasil optimal, tapi O(N!) — tidak mungkin untuk 168 mahasiswa
- Greedy: hasil mendekati optimal, O(N × M × iterasi) — cepat dan praktis

Pada data aktual thesis (168 × 14), solver selalu menemukan solusi valid tanpa RuntimeError — artinya constraint kapasitas selalu terpenuhi.

## Evaluation Metrics

### Setup: Data Dummy

**5 mahasiswa, 5 supervisor** (disederhanakan dari 168 × 14):

| Mahasiswa | Ground Truth (GT) |
| --- | --- |
| M1 | D001 |
| M2 | D003 |
| M3 | D002 |
| M4 | D001 |
| M5 | D005 |

**Score matrix** hasil cosine similarity (baris = mahasiswa, kolom = supervisor):

```
         D001   D002   D003   D004   D005
M1:    [ 0.85,  0.72,  0.60,  0.41,  0.30 ]
M2:    [ 0.55,  0.48,  0.79,  0.62,  0.51 ]
M3:    [ 0.40,  0.88,  0.71,  0.55,  0.33 ]
M4:    [ 0.70,  0.65,  0.80,  0.45,  0.38 ]
M5:    [ 0.61,  0.53,  0.44,  0.39,  0.77 ]
```

### Step 1  Tentukan Rank per Mahasiswa

Untuk setiap mahasiswa, urutkan supervisor dari skor tertinggi → cari posisi GT.

**M1** (GT = D001):

```
Urutan skor: D001(0.85) > D002(0.72) > D003(0.60) > D004(0.41) > D005(0.30)
Rank D001 = 1  ✅
```

**M2** (GT = D003):

```
Urutan skor: D003(0.79) > D004(0.62) > D001(0.55) > D005(0.51) > D002(0.48)
Rank D003 = 1  ✅
```

**M3** (GT = D002):

```
Urutan skor: D002(0.88) > D003(0.71) > D004(0.55) > D001(0.40) > D005(0.33)
Rank D002 = 1  ✅
```

**M4** (GT = D001):

```
Urutan skor: D003(0.80) > D001(0.70) > D002(0.65) > D004(0.45) > D005(0.38)
Rank D001 = 2  ⚠️  (kalah dari D003)
```

**M5** (GT = D005):

```
Urutan skor: D005(0.77) > D001(0.61) > D002(0.53) > D003(0.44) > D004(0.39)
Rank D005 = 1  ✅
```

**Tabel rank hasil:**

| Mahasiswa | GT | Rank GT |
| --- | --- | --- |
| M1 | D001 | **1** |
| M2 | D003 | **1** |
| M3 | D002 | **1** |
| M4 | D001 | **2** |
| M5 | D005 | **1** |

### Step 2 Hitung Tiap Metrik

### MRR (Mean Reciprocal Rank)

Formula: `MRR = rata-rata(1 / rank)`

```
M1: 1/1 = 1.000
M2: 1/1 = 1.000
M3: 1/1 = 1.000
M4: 1/2 = 0.500
M5: 1/1 = 1.000

MRR = (1.000 + 1.000 + 1.000 + 0.500 + 1.000) / 5
    = 4.500 / 5
    = 0.900
```

> **Interpretasi**: Rata-rata supervisor GT ada di posisi ke-1.11 dari 5 kandidat.
> 

### Hit@1

Formula: `1 jika rank = 1, else 0`

```
M1: rank=1 → 1
M2: rank=1 → 1
M3: rank=1 → 1
M4: rank=2 → 0  ← miss
M5: rank=1 → 1

Hit@1 = (1+1+1+0+1) / 5 = 4/5 = 0.800 (80%)
```

> **Interpretasi**: 80% mahasiswa, supervisor GT-nya muncul di rekomendasi teratas.
> 

### Hit@5

Formula: `1 jika rank ≤ 5, else 0`

```
Semua rank ≤ 5 (max rank = 2, total supervisor = 5)
M1: 1, M2: 1, M3: 1, M4: 1, M5: 1

Hit@5 = 5/5 = 1.000 (100%)
```

> **Interpretasi**: Semua supervisor GT masuk dalam top-5 rekomendasi.
> 

### nDCG@5

Formula: `1 / log2(rank + 1)` jika rank ≤ 5, else 0

```
M1: 1/log2(1+1) = 1/log2(2) = 1/1.000 = 1.000
M2: 1/log2(1+1) = 1/1.000   = 1.000
M3: 1/log2(1+1) = 1/1.000   = 1.000
M4: 1/log2(2+1) = 1/log2(3) = 1/1.585 = 0.631  ← penalti rank 2
M5: 1/log2(1+1) = 1/1.000   = 1.000

nDCG@5 = (1.000 + 1.000 + 1.000 + 0.631 + 1.000) / 5
        = 4.631 / 5
        = 0.926
```

> **Interpretasi**: nDCG memberi penalti progressif — rank 2 → 0.631, rank 5 → 0.387.
> 

### nDCG@10

Formula identik dengan nDCG@5, threshold diperluas ke rank ≤ 10.

Di contoh ini hasilnya sama = **0.926** karena semua rank ≤ 5.

Perbedaan terasa jika ada mahasiswa dengan rank > 5:

```
Contoh mahasiswa dengan rank = 7:
  nDCG@5  → 0      (rank > 5, tidak dihitung)
  nDCG@10 → 1/log2(7+1) = 1/3.000 = 0.333  (masih dihitung)
```

### Avg Rank

Formula: `rata-rata(rank)`

```
Avg Rank = (1 + 1 + 1 + 2 + 1) / 5 = 6/5 = 1.2
```

> **Interpretasi**: Rata-rata supervisor GT ada di posisi 1.2 — sangat dekat ke top.
> 

## Step 3 — Assignment Match Rate (Post-Solver)

Dihitung **setelah greedy solver** menentukan penugasan final, bukan dari ranking.

Misal output solver:

| Mahasiswa | GT | Assigned | Match? |
| --- | --- | --- | --- |
| M1 | D001 | D001 | ✅ |
| M2 | D003 | D003 | ✅ |
| M3 | D002 | D002 | ✅ |
| M4 | D001 | D003 | ❌ (D001 sudah penuh, solver paksa pindah ke D003) |
| M5 | D005 | D005 | ✅ |

```
Match Rate = 4/5 = 0.800 (80%)
```

> **Kunci**: M4 di ranking merekomendasikan D001 tapi solver tidak bisa karena constraint kapasitas.
> 
> 
> Inilah mengapa **Match Rate selalu ≤ Hit@1** pada sistem dengan constraint kapasitas.
> 

## Ringkasan Hasil Dummy

| Metrik | Nilai Dummy | Baseline Acak (5 sup) |
| --- | --- | --- |
| MRR | **0.900** | 0.457 |
| Hit@1 | **80%** | 20% |
| Hit@5 | **100%** | 100% |
| nDCG@5 | **0.926** | 0.423 |
| Avg Rank | **1.2** | 3.0 |
| Match Rate | **80%** | ~20% |

## Nilai Aktual Run 26 (168 mahasiswa, 14 supervisor)

| Metrik | Run 26 (bge-m3) | Baseline Acak (14 sup) |
| --- | --- | --- |
| MRR | **0.711** | 0.232 |
| Hit@1 | **71.4%** | 7.1% |
| Hit@5 | **92.9%** | 35.7% |
| nDCG@5 | — | 0.211 |
| Avg Rank | — | 7.5 |
| Match Rate | **53%** | ~7.1% |

Selisih MRR: **0.711 vs 0.232** → sistem 3× lebih baik dari random baseline.

Ini argumen kuantitatif utama validitas pendekatan semantic similarity.

- Cara menghitung cosine similaritynya gimana
- Cara Ngitung evaluasi nya gimana.

⁠Cara menjelaskan setiap matriks penilaian retrievalnya (cara ngitung, variable yang bisa mempengaruhi skornya)

Justifikasi Pemilihan Metrik Evaluasi

MRR (Mean Reciprocal Rank)
Mengukur seberapa tinggi dosen ground truth muncul dalam ranking. Cocok karena mahasiswa biasanya hanya memperhatikan rekomendasi teratas — sistem yang menaruh dosen GT di rank 1 jauh lebih berguna dari yang menaruhnya di rank 10. Dipakai di AVRD dan SPECTER sebagai metrik utama ranking.

Hit@1
Ukuran paling ketat: apakah rekomendasi pertama langsung benar. Relevan karena dalam konteks penugasan skripsi, mahasiswa sering tidak punya kuasa penuh memilih — rekomendasi teratas yang digunakan sistem.

Hit@5
Mengakomodasi realita bahwa sistem menyajikan daftar rekomendasi, bukan hanya 1 pilihan. Jika dosen GT masuk top-5, mahasiswa masih punya kesempatan memilihnya. Berguna untuk mengukur "coverage" rekomendasi.

nDCG@5
Dibandingkan Hit@5 yang binary, nDCG memberi penalti progresif — dosen GT di rank 2 lebih baik dari rank 5. Standar industri di BEIR dan MTEB benchmark untuk evaluasi retrieval.

Avg Rank
Metrik intuitif untuk penguji non-teknis: "rata-rata, dosen yang benar ada di posisi berapa?" Mudah dijelaskan dan mudah dipahami tanpa formula matematis.

Match Rate (post-solver)
Metrik unik yang tidak ada di AVRD, BERT Hybrid, maupun SPECTER. Mengukur hasil setelah greedy solver menerapkan constraint kapasitas (max 12 mahasiswa/dosen). Ini membedakan kualitas embedding model dari kualitas sistem penugasan secara end-to-end — dua hal yang berbeda dan keduanya penting untuk dilaporkan.

---

Kalimat justifikasi untuk sidang:

▎ "Kami memilih kombinasi metrik ranking (MRR, Hit@K, nDCG) yang sudah menjadi standar di literatur information retrieval, ditambah Avg Rank untuk keterbacaan, dan Match Rate sebagai metrik domain-spesifik yang mencerminkan constraint kapasitas nyata dalam sistem penugasan skripsi."

apa itu gini coefficient

apa itu random base line