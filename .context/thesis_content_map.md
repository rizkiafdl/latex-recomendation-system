# Thesis Content Map — Topic Index
> Purpose: what is *written* in each chapter (topics, arguments, named items). Not a file structure map — see project-overview.md for that.
> Last crawled: 2026-06-07

---

## Metadata (Awal_konfigurasi.tex)

| Field | Value |
|---|---|
| Title (ID) | PENGEMBANGAN SISTEM REKOMENDASI FACULTY SUPERVISOR UNTUK MAHASISWA MENGGUNAKAN SEMANTIC SIMILARITY BERBASIS WEBSITE DI UNIVERSITAS XYZ |
| Title (EN) | DEVELOPMENT OF A FACULTY SUPERVISOR RECOMMENDATION SYSTEM FOR STUDENTS USING WEBSITE-BASED SEMANTIC SIMILARITY AT XYZ UNIVERSITY |
| Authors | Muhammad Rizki Afdolli (2602139141), Rakha Naufal Azizi (2602187241), Theofilus Adhi Septian (2602096230) |
| Supervisors | Budi Juarto S.T., M.Kom (D6670); Prof. Dr. Ir. Derwin Suhartono S.Kom., MTI |
| Program | Computer Science — School of Computer Science, BINUS, 2026 |

---

## Abstracts

### Indonesian (abstrak.tex) — written
- Problem: manual student-to-internship-supervisor mapping in Enrichment Program at XYZ University → inefficiency, inconsistency, expertise mismatch.
- Proposed system: semantic similarity + rule-based logic as decision support (EPC retains final authority).
- Method: NLP on student profiles + supervisor expertise text → cosine similarity → quota-constrained ranking.
- Evaluation: compare against ground truth from multiple EPC manual mappings + 1-year historical data.
- Outcome: prototype shows alignment with manual mapping, improved efficiency/consistency/transparency.
- Keywords: Sistem Rekomendasi Faculty Supervisor, Semantic Similarity, Rule-Based Logic, NLP, Decision Support System, Program Enrichment.

### English (abstrak.tex) — written (embedded inside abstrak.tex, after the Indonesian abstract)
- Same content as Indonesian abstract, fully translated.
- Keywords: Faculty Supervisor Recommendation System, Semantic Similarity, Rule-Based Logic, Natural Language Processing, Decision Support System, Enrichment Program.

### abstract.tex — **DISABLED** (`% \include{abstract}` commented out in Skripsi.tex line 124)
- File exists but is not compiled into the PDF.
- Contains a duplicate/standalone English abstract with `% !TEX root = Skripsi.tex` magic comment.

---

## BAB 1 — Pendahuluan (bab1.tex) — written

### 1.1 Latar Belakang
- Enrichment Program at XYZ University: practice-based learning (internship, research, entrepreneurship) overseen by Faculty Supervisor.
- Current process: manual spreadsheet mapping, 1–3 weeks/batch, up to 20 revision rounds, no expertise alignment.
- Prior work cited: Fan et al. (2021) ML automation; Rismanto et al. (2020) TF-IDF + cosine (75% accuracy) — limitation: no semantic capture.
- Proposed: semantic similarity via text embeddings for student↔supervisor matching; ground truth from EPC + historical data; decision support role.

### 1.2 Rumusan Masalah
Four research questions: (1) current process analysis, (2) design/development of system, (3) agreement level vs. ground truth, (4) efficiency improvement.

### 1.3 Ruang Lingkup Penelitian
- Scope: internship track only within Enrichment Program at XYZ.
- Text-based semantic similarity; evaluation vs. EPC ground truth + historical data.
- Deliverable: working prototype with UI + backend.
- Exclusions: academic policy outside Faculty Supervisor mapping.

### 1.4 Tujuan dan Manfaat
- Objectives: analyze manual process → develop system → evaluate vs. ground truth → deliver prototype.
- Benefits: faster mapping, fewer revisions, better expertise alignment, foundation for future systems.

### 1.5 Metode Penelitian (overview)
Six stages: literature review → data collection → preprocessing → system development → evaluation → prototype.
- **1.5.1** Data Collection: institutional documents, historical mapping (1 year), EPC ground truth.
- **1.5.2** Preprocessing: cleaning, normalization, tokenization, stop-word removal.
- **1.5.3** Recommendation System: semantic similarity ranking, quota/workload constraints, decision support.
- **1.5.4** Prototype: UI + backend workflow demo.
- **1.5.5** Evaluation: compare vs. ground truth; analyze quality and efficiency gains.

### 1.6 Sistematika Penulisan
Brief 5-chapter structure overview.

---

## BAB 2 — Tinjauan Pustaka (bab2.tex) — written

### Landasan Teori (Theoretical Background)

| Concept | Key Points |
|---|---|
| Sistem Rekomendasi | Decision support selecting best option; Ricci et al. (2015) |
| Artificial Intelligence | Systems mimicking human intelligence; Russell & Norvig (2021) |
| Machine Learning | Pattern learning from data; Mitchell (1997) |
| Deep Learning | Multi-layer neural networks for unstructured data; LeCun et al. (2015) |
| NLP | Processing/understanding human language; Jurafsky & Martin (2023) |
| Text Mining | Extracting patterns/knowledge from text documents |
| Text Preprocessing | Minimal: case folding + non-alphanum removal + whitespace collapse; transformer models handle morphology internally |
| Text Representation | Numeric conversion of unstructured text; Manning et al. (2008) |
| TF-IDF | Term frequency + inverse document frequency; Salton & Buckley (1988); used as conceptual baseline — cannot capture semantic meaning |
| Word Embedding | Static word vectors (Word2Vec, GloVe, FastText); limitation: no sentence context |
| Transformer Embedding | Context-aware via self-attention; Vaswani et al. (2017); Devlin et al. (2019) |
| BERT Embedding | Bidirectional contextual representations; Devlin et al. (2019) |
| Semantic Similarity | Meaning equivalence beyond literal match; Mihalcea et al. (2006) |
| Cosine Similarity | Angle between vectors; Han et al. (2012); formula: (A·B)/(‖A‖×‖B‖) |
| Text/Model Embedding | Semantic numeric vectors; Reimers & Gurevych (2019) |

**Embedding Model Candidates (3):**
- **BAAI/bge-m3** — Multi-Functionality (dense/sparse/multi-vector), 100+ languages, 8192 tokens; Chen et al. (2024)
- **Qwen3-Embedding-0.6B** — 600M params, multilingual, semantic search optimized; Alibaba Cloud
- **intfloat/multilingual-e5-large-instruct** — ~560M params, 90+ languages, instruction-tuned; Wang et al. (2024) — *default model chosen*

**Evaluation Metrics:**
- Hit@K: binary — is ground truth in top-K?
- nDCG@K: ranking quality with logarithmic position discount; DCG/IDCG formula
- Recall@K: relevant items surfaced in top-K; Herlocker et al. (2004)
- Precision@K: relevant items among top-K
- MRR: reciprocal rank of first relevant item; Voorhees (1999)
- Cosine Similarity Score: vector angle measure
- Assignment Match Rate: final assignments matching ground truth / total
- SUS (System Usability Scale): 10-question usability questionnaire; Brooke (1996); 0–100 score

**Supporting Technologies:**
- Python (backend, AI/ML), JavaScript (frontend)
- Flask + Jinja2 (web framework, server-side rendering)
- PyTorch (transformer model execution)
- SQLite (embedded DB — no server; on-the-fly NumPy similarity), SQLAlchemy (ORM)
- Sentence-Transformers (embedding generation, batch + GPU/CPU auto-detect)
- Port Forwarding (external access during testing)
- On-Premises Deployment (single Docker container; local model execution; data governance)
- UML: Use Case Diagram, Activity Diagram, Class Diagram, ERD

### Penelitian Terkait (Related Research — longtable, 6 studies)

| Author | Topic | Method | Key Result |
|---|---|---|---|
| Tang et al. (2016) | Thesis topic prediction + supervisor recommendation | Naive Bayes, TF-IDF, Cosine Similarity | 88.69% acc without TF-IDF; with TF-IDF worse |
| Huang et al. (2019) | Semantic similarity supervisor recommendation | Word2Vec, Cosine Similarity | >15% relevance improvement over keyword matching |
| Aljohani & Davis (2020) | Student-supervisor text mining matching | Text Mining, Vector Space Model | >40% time reduction vs. manual |
| Rahman et al. (2023) | Contextual embedding supervisor recommendation | Contextual Embedding, Cosine Similarity, Ranking Metrics | (detailed results not in table) |
| Li et al. (2022) | Semantic search supervisor recommendation | Transformer Embedding, Semantic Search | (detailed results not in table) |
| Zhang et al. (2021) | Deep learning academic advisor recommendation | BERT Embedding, Deep Learning | (detailed results not in table) |

Synthesis: TF-IDF → Word Embedding → Transformer evolution; prior work lacks operational web integration + institutional constraints. This research adds: quota/workload rules + full web prototype + MRR/Recall@K/SUS evaluation.

---

## BAB 3 — Metode Penelitian (bab3.tex) — written

### 3.1 Kerangka Berpikir
- Problem: manual EPC mapping is slow, subjective, opaque.
- Solution: **hybrid scoring** = semantic similarity + rule-based boost (label affinity) + company group bonus → greedy capacity-constrained solver.
- Figure 3.1: conceptual framework diagram.

### 3.2 Metodologi Penelitian

#### 3.2.1 Analisis
- **Problem Analysis**: no structure/transparency in manual process; uneven supervision load.
- **Actors**: EPC only (students/supervisors are data entities, not users).
- **Functional Requirements (7)**: manage student data, manage supervisor profiles, process text, identify suitability, rank supervisors, validate slot availability, display recommendations.
- **Non-Functional Requirements (4)**: web-based, simple UI, transparent/consistent recommendations, structured data.
- **Data Analysis**: student text (position, company, role); supervisor text (academic characteristics, historical internship mentoring, thesis topic recommendations); → semantic similarity justified.

#### 3.2.2 Perencanaan
- Scope: XYZ org, 2024 historical data; decision support only; website; no model retraining.

#### 3.2.3 Perancangan Model Rekomendasi
- **Supervisor representation**: two document sources — historical mentoring data + thesis topic recommendations; aggregated per supervisor; stored as embeddings.
- **Student representation**: position + role + company + category → single embedding.
- **Embedding candidates evaluated**:
  1. Qwen3-Embedding-0.6B (MRL, 32K context, 600M params)
  2. EmbeddingGemma-300M (100+ langs, 2K limit, MRL)
  3. Jina-embeddings-v3 (8192 tokens, task adapters, MRL)
  *(Note: bab2 lists BAAI/bge-m3, Qwen3, multilingual-e5 — bab3 lists different candidates; bab4 uses BAAI/bge-m3, Qwen3, multilingual-e5 for actual runs)*
- **Similarity**: cosine similarity → max over supervisor documents → top-5 ranking.
- **Slot validation**: greedy sequential allocation; unallocated if no slots in top-5.
- **Evaluation metrics planned**: Recall@5, nDCG@5; proportion allocated; rank distribution.

#### 3.2.4 Perancangan UML

**Use Cases (6):**
1. **Register** — EPC creates account (username ≥3 chars, password ≥6 chars, match confirmation).
2. **Login** — EPC authenticates; generic error message (no username/password distinction).
3. **Import Data Mahasiswa** — Upload Excel with columns: STUDENT ID, TRACK, PARTNER/LECTURER, POSITION/TOPIC, WORK SCHEMA, GPA; upsert logic.
4. **Kelola Data Faculty Supervisor (Supervisor Studio)** — Add/update supervisor profiles + semantic keywords; export config.
5. **Kelola Label & Affinity Rules (Rules Studio)** — Configure semantic labels (description, cosine threshold 0–1, niche flag); manage supervisor-label affinity matrix (boost values); preview scores; cache invalidation.
6. **Trigger Proses Rekomendasi** — Full pipeline: embedding → similarity → rule boost → group bonus → capacity planning → greedy solver → evaluation; model selection; enable/disable boost components; fallback to TF-IDF on embedding failure.
7. **Lihat Hasil Rekomendasi** — View run detail: content-based + hybrid metrics, capacity distribution, mismatch list, searchable student-supervisor table.

**Diagrams**: Use Case (Fig 3.2), Class Diagram (Fig 3.3 — embedding/hybrid scoring/solver/evaluation classes), ERD (Fig 3.4 — 7 SQLite entities).

#### 3.2.5 Tahapan Implementasi
Data collection → preprocessing (5 steps: cleaning, case folding, tokenization, stopword removal, normalization) → text representation (embeddings) → similarity calculation → recommendation system integration → web deployment.

---

## BAB 4 — Hasil dan Pembahasan (bab4.tex) — partially written

### 4.1 Testing Environment
- **Hardware**: Apple M4 Pro (12-core), 24 GB RAM, 20-core GPU + 16-core ANE, 1 TB SSD.
- **Software**: macOS Sequoia 15, Python 3.12, Flask 3.1.0, SQLAlchemy 2.0.49 + SQLite, sentence-transformers.
- **Models tested**: BAAI/bge-m3, Qwen/Qwen3-Embedding-0.6B, intfloat/multilingual-e5-large-instruct.
- **Tools**: pandas, numpy, openpyxl, VS Code.

### 4.2 Hasil

#### 4.2.1 Evaluasi Model — written (with placeholders)
- 168/171 students evaluated (3 excluded: no valid `current_supervisor_code`).
- Two metric levels: retrieval (MRR, Hit@1/5, NDCG@5/10, avg_rank) and assignment (Match Rate).
- **Table 4.2.1 — Perbandingan Metrik Evaluasi Antar Model** (values filled, model names are `[LENGKAPI]` placeholders):
  - Model A (Run 5): MRR 0.585, Hit@1 0.417, Hit@5 0.804, NDCG@5 0.622, NDCG@10 0.668, Avg Rank 3.32, Match Rate 0.357
  - Model B (Run 6): MRR 0.474, Hit@1 0.298, Hit@5 0.708, NDCG@5 0.505, NDCG@10 0.576, Avg Rank 4.20, Match Rate 0.357
  - Model C (Run 9): MRR 0.469, Hit@1 0.304, Hit@5 0.649, NDCG@5 0.483, NDCG@10 0.563, Avg Rank 4.60, Match Rate 0.381
  - Random baseline (M≈14): MRR ~0.23, Hit@1 ~0.07, Hit@5 ~0.36, NDCG@5 ~0.21
- **Table 4.2.2 — Rata-rata Skor Kemiripan Antar Model** (values filled, model names placeholders):
  - Model A: Avg Sim@1 0.619, Avg True Sim 0.596, Spread 0.023
  - Model B: Avg Sim@1 0.692, Avg True Sim 0.624, Spread 0.068
  - Model C: Avg Sim@1 0.871, Avg True Sim 0.857, Spread 0.014

#### 4.2.2 Hasil Analisis Model dan Pemilihan Model Akhir — written (with placeholders)
- Model A selected: best on all retrieval metrics consistently.
- Model C anomaly: highest match rate (0.381) but worst retrieval — explained by near-uniform similarity scores (spread 0.014); capacity solver coincidentally aligned with historical data on a flat score distribution.
- Principle: retrieval quality (ranking position) is primary criterion; match rate is complementary but misleading in isolation.
- Model A is deterministic and reproducible.
- **Still needed**: fill model names (A = ?, B = ?, C = ?).

#### 4.2.3 Aplikasi Web — written
- Login Page: credential form (username + password); redirect to dashboard on success; registration button.
- Register Page: full name, username, password, confirm password.
- Dashboard: summary stats (total students, supervisors, total runs, last accuracy, evaluation results); buttons: Generate New Run, Open Detail, Inspect Rows, Export Excel; Quality Trend chart (Match Rate, Hybrid MRR, Content MRR tooltips); Snapshot info.
- Data Center: ETL pipeline trigger; file upload (Excel); summary cards; Generate New Run button.
- Run History: chronological run table (Run ID, Created, Students, Supervisor, Model, Solver, Objective, Match Rate, Hybrid MRR); Details Row + Export Data per run.
- Run Details: per-student table (NIM, Name, Track, Partner, Topic, Current Supervisor, Recommended Supervisor, Final Score, Rule Match); filter by name/topic/supervisor.
- Supervisor Studio: searchable dropdown + Load Supervisor; Export Config Excel; Add Supervisor form; Keywords Studio (chip add/remove/save); Supervisor Table.
- Rules Studio: manages cosine similarity threshold + semantic label descriptions (controls mapping to categories like `apple_mobile`, `binus_bandung`).

### 4.3 Evaluasi (Black Box Testing — Equivalence Partitioning) — written
Tests for: Login, Register, Dashboard, Data Center, Generate New Run, Run History, Supervisor Studio, Rules Studio. All scenarios: **Pass**.

**Key scenarios covered:**
- Login: valid credentials → dashboard; wrong password/email → "Kredential Salah".
- Register: valid data → saved; mismatched/invalid → "Data tidak sesuai".
- Dashboard: all 11 navigation/button/data-display scenarios.
- Data Center: empty upload, wrong format, valid upload, summary cards, Generate New Run.
- Generate New Run: model selection (orange highlight), toggle rule boosts (single + multiple), cancel, execute.
- Run History: table display (descending), Details Row, Export Data, Generate New Run.
- Supervisor Studio: dropdown search, Load Supervisor, Export Config, Add Keyword, remove chip, Save Keywords.
- Rules Studio: same 6 scenarios as Supervisor Studio (table content appears identical — possible copy-paste).

---

## BAB 5 — Simpulan dan Saran (bab5.tex) — **STUB**
- Section 5.1 Simpulan: lipsum placeholder.
- Section 5.2 Saran: lipsum placeholder.
- **Not written yet.**

---

## Open Placeholders / TODOs

| Location | Placeholder | What's needed |
|---|---|---|
| bab4.tex §4.2.1–4.2.2 | `[LENGKAPI: nama model A/B/C]` | Actual embedding model names for Run 5, 6, 9 |
| bab4.tex §4.2.1 | `[LENGKAPI: jumlah model]` | Number of candidate models (likely "tiga") |
| bab4.tex table:tab4.2.1/4.2.2 | Table numbering | Verify final thesis table numbers |
| abstract.tex | Disabled (`% \include{abstract}`) | English abstract is in abstrak.tex; abstract.tex is a redundant file — can be deleted or left as-is |
| bab5.tex | Both sections | Write Simpulan and Saran |
| bab4.tex Rules Studio prose | Copy-paste from Supervisor Studio | Fix §4.2.3 Rules Studio description (currently duplicates Supervisor Studio text) |
