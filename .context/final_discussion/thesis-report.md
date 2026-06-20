# Thesis Report: Comprehensive Analysis
**Sistem Rekomendasi Faculty Supervisor Berbasis Semantic Similarity**
**XYZ University — Enrichment Program**

_Last crawled: 2026-06-20 (all 5 chapters + abstrak re-read from source files)_

---

## 1. What This Thesis Is About

This thesis develops a **web-based Decision Support System (DSS)** that automates the matching of students to Faculty Supervisors (FS) in XYZ University's Enrichment Program (internship track). The current process is manual — a spreadsheet-driven procedure run by Enrichment Program Coordinators (EPC) — and the thesis proposes replacing its core logic with **zero-shot multilingual semantic similarity** using transformer-based text embeddings, augmented by a greedy capacity-constrained allocation solver.

The system does **not** replace EPC judgment; it produces ranked recommendations and final assignments as decision support, with EPC retaining final authority.

---

## 2. Problems Being Solved (Rumusan Masalah — BAB 1)

Three formal research questions:

| # | Research Question |
|---|---|
| RQ1 | How to design and develop a Faculty Supervisor recommendation system for internships based on semantic similarity? |
| RQ2 | How high is the match rate between system recommendations and the ground truth obtained from EPC manual matching? |
| RQ3 | How can the developed recommendation system help improve the efficiency of the Faculty Supervisor mapping process? |

**Root causes identified (BAB 1 Latar Belakang):**
- Manual process takes **1–3 weeks per batch** per faculty
- Assignments are often random — no structured competency matching
- Up to **20 revision rounds per batch** due to errors and conflicts
- EPC load (cognitive + time) is high and unsupported by tooling
- No mechanism to ensure even distribution of supervisor load
- No auditability or transparency in the decision trail

---

## 3. Objectives (Tujuan — BAB 1)

1. Analyze problems in the current manual mapping process
2. Develop a semantic similarity–based FS recommendation system
3. Evaluate system recommendations against ground truth from EPC manual matching and historical data
4. Produce a working prototype (web frontend + backend) for EPC use

---

## 4. Literature Foundation (BAB 2)

### Theoretical Ladder
The thesis establishes a clean conceptual chain:
```
AI → Machine Learning → Deep Learning → NLP → Text Mining
→ Text Preprocessing → Text Representation
→ TF-IDF (baseline) → Word Embedding (Word2Vec/GloVe/FastText)
→ Transformer-Based Embedding → BERT
→ Semantic Similarity → Cosine Similarity
→ Model Embedding (BGE-M3, Qwen3-0.6B, mE5-large-instruct)
```

Text preprocessing: BAB 2 specifies a **minimalist 3-step approach** — case folding, non-alphanumeric character removal, collapse whitespace — implemented via `normalize_text()`. Deliberately omits stemming and stop word removal to preserve contextual signals for transformer models.

### Evaluation Metrics Grounded
- **MRR** — measures how fast the correct supervisor appears in ranked list
- **Hit@1 / Hit@5** — binary hit within top-K
- **nDCG@5 / nDCG@10** — position-weighted ranking quality
- **Precision@K** — proportion of relevant in top-K
- **Recall@K** — coverage of relevant results
- **Cosine Similarity Score** — raw similarity measure
- **Assignment Match Rate** — end-to-end match against historical assignments

### Supporting Technology Stack (BAB 2 sections)
- **Python** (de facto backend language; NumPy, PyTorch, Scikit-learn ecosystem)
- **JavaScript** (frontend interactivity)
- **Flask + Jinja2** (micro web framework; server-side rendering)
- **PyTorch** (deep learning framework for transformer model execution)
- **SQLite** (embedded file-based DB; no vector store needed; similarity computed on-the-fly via NumPy matrix ops)
- **SQLAlchemy** (ORM; Core + ORM layers; supports SQLite/PostgreSQL/MySQL)
- **Sentence-Transformers** (batch encoding with L2 normalization; cosine similarity via dot product)
- **Port Forwarding** (used during testing/deployment for local service access)
- **On-Premises Deployment** (concept: Docker container, local infrastructure) — **Note**: BAB 3 actual deployment is on cloud VPS (Tencent Cloud), creating a minor BAB 2 ↔ BAB 3 discrepancy

### UML Subsections (BAB 2)
Definitions and symbol tables for: Use Case Diagram, Activity Diagram, Class Diagram, Entity Relationship Diagram — all citing `dennis2020systems`.

### Model Waterfall (BAB 2)
5 phases per Pressman (`pressman2010se`): Communication → Planning → Modeling → Construction → Deployment. Justified by: stable requirements from EPC data, strong sequential dependency between phases, prototype scope well-defined from the start. Supporting citations: `pargaonkar2023sdlc`, `saravanos2023waterfall`.

### Algoritma Greedy (BAB 2)
Positioned as heuristic solution to weighted bipartite matching (Hungarian Algorithm has O(n³) complexity, impractical for large scale). For N≈170 students, M=14 supervisors, greedy is valid and deterministic. Supporting citations: Duvignau et al. 2023 (`duvignau2023greediness` — bipartite assignment guarantees), Ramotsisi et al. 2022 (`ramotsisi2022optimization` — student-supervisor project assignment), Maashi 2020 (`maashi2020greedy` — King Saud University greedy linear heuristic).

### Related Research (6 Studies)
| Paper | Method | Relevance |
|---|---|---|
| Zhang et al. (2016) | Personality matching | Personality-aware supervisor rec; 12–15% mutual satisfaction improvement |
| Cohan et al. (2020) — SPECTER | BERT + Citation Graph | Scientific doc similarity; Macro F1/Accuracy 81–84% |
| Nagarajan et al. (2025) | TF-IDF + BERT Hybrid | Academic collaboration rec; Precision 99.14%, Recall 92.48%, F1 95.69% |
| Wang et al. (2025) — AVRD | Chinese BERT + SimCSE | Advisor recommendation; 70% of 170 students found ideal advisor |
| Sabilillah et al. (2024) | BERT + Cosine Similarity | Thesis supervisor rec; accuracy 89.99% |
| Dasri et al. (2025) | MapReduce K-Skyband | Two-way thesis supervisor rec; 8× faster than Block Nested Loop |

**Gap identified:** None of the prior work uses the newest generation of zero-shot multilingual dense retrieval models (BGE-M3, mE5-large-instruct, Qwen3-Embedding) comparatively for a bilingual (Indonesian–English) academic supervisor recommendation context, nor integrates quota constraints into a web DSS.

**mE5-large described as "default" in BAB 2:** Line 163 of bab2.tex explicitly states mE5-large-instruct is the system's default model. This is a pre-experiment assumption later corrected by BAB 4 evidence (bge-m3 is superior on retrieval metrics).

---

## 5. Methodology (BAB 3) — Does It Answer the Research Questions?

### Research Framework
The thinking framework (Kerangka Berpikir) flows logically:
```
Problem Analysis → Planning → Recommendation Model Design →
Evaluation (meet criteria?) → Yes: UML Design → Web Prototype
                           → No: back to Planning
```

### Methodology Model: Waterfall (BAB 3)
Mapped to BAB 3 subsections via explicit table:
| Waterfall Phase | BAB 3 Coverage |
|---|---|
| Analisis Kebutuhan | Analisis Permasalahan, Analisis Aktor, Analisis Kebutuhan, Analisis Data |
| Perancangan | Perencanaan, Perancangan Model, Kandidat Model, Perancangan UML |
| Implementasi | Tahapan Implementasi — Implementasi dan Deployment |
| Pengujian | Black Box Testing (executed in BAB 4) |
| Pemeliharaan | Out of scope for prototype |

### System Actor Analysis
- **EPC** = sole actor; administrator role
- **Students and Faculty Supervisors** = data entities, NOT system actors

### Functional Requirements (7 items)
1. Manage student data (position, company)
2. Manage faculty supervisor profiles
3. Process text data from both entities
4. Identify compatibility between student and supervisor
5. Rank supervisors by compatibility
6. Consider supervisor slot availability
7. Display recommendation results per student

### Data Sources
**Student data (text input — 3 fields used for embedding):**
- `track` (program studi)
- `position_topic` (posisi dan topik magang)
- `work_schema` (skema kerja)
- `partner_lecturer` (company name) — NOT in embedding text; used only as company group bonus grouping key

**Supervisor data (single document per supervisor):**
- Base: `profile_keywords` from `supervisors` table (comma-separated competency keywords)
- If `extra_docs=True`: enriched with (a) historical mentoring data (position, company, job description of previously mentored students) and (b) thesis recommendation data (title, description, problem statement, expected output, thesis type)
- Each supervisor has **one profile document** built at inference time (not multi-document; no max-pooling)

### 8-Stage Pipeline (addresses RQ1 comprehensively)
1. **Excel Import** — EPC uploads student data via web UI; upsert to DB via `excel_io.py`
2. **Text Document Construction** — `rules.py` builds documents: student = track+position_topic+work_schema; supervisor = profile_keywords + optional extra_docs enrichment
3. **Embedding + Similarity** — `embedding.py` encodes all docs with same model; L2 normalization applied; cosine similarity via dot product → N×M matrix. Fallback order: SentenceTransformer → TF-IDF → Token Overlap (Jaccard)
4. **Hybrid Scoring** — similarity matrix × weight (default 1.0) + optional company group bonus. Bonus requires: ≥2 students same company, topic diversity ≤6 tokens, score gap ≥0.08 between top-2 supervisors; bonus decays logarithmically with group size
5. **Capacity Planning** — default range 10–12 students/supervisor; overflow/underflow auto-adjusted; overflow extra slots go to priority codes first, then round-robin
6. **Greedy Solver** — 2-phase: (a) Initialize: argmax assignment; (b) Phase 1: reduce overfull by moving students with minimum penalty; (c) Phase 2: fill underfull from donors. RuntimeError if constraints unmet after max iterations
7. **Evaluation** — 3 passes: content-based matrix, hybrid matrix, final assignment. Metrics: MRR, Hit@1, Hit@5, NDCG@5, NDCG@10, Precision@5, Match Rate
8. **Save & Export** — stored to `recommendation_runs` + `recommendations`; top-5 as JSON in `rankings_json`; 2 Excel formats: standard (3 sheets: recommendations, summary, evaluation) or detailed (4 sheets + rankings per student)

### 3 Candidate Models
| Model | Context Window | Parameters | Key Strength |
|---|---|---|---|
| BAAI/bge-m3 | 8,192 tokens | ~570M | Multi-retrieval (dense+sparse+multi-vector), 100+ languages; used in dense mode only |
| Qwen3-Embedding-0.6B | 32,000 tokens | ~600M | Longest context, lightest model, MRL support (32–1024 dim flexible) |
| intfloat/multilingual-e5-large-instruct | 512 tokens | ~560M | Instruction-following, XLM-RoBERTa base, 1024-dim embedding; set as **system default** |

All models used zero-shot (no fine-tuning) on the same pipeline, ensuring fair comparison. L2 normalization applied by `embedding.py` for all models.

### 12 Use Cases (Use Case Diagram)
UC1=Register, UC2=Login, UC3=Import Data Mahasiswa, UC4=Kelola Data Faculty Supervisor, UC5=Kelola Keywords Supervisor, UC6=Lihat Dashboard, UC7=Trigger Proses Rekomendasi, UC8=Lihat Riwayat Run, UC9=Lihat Hasil Rekomendasi, UC10=Export Hasil ke Excel, UC11=Logout, UC12=Export Konfigurasi Supervisor

### Activity Diagrams (8 diagrams)
Login, Register, Import Data Mahasiswa, Kelola Data Faculty Supervisor, Kelola Keywords Supervisor, Trigger Proses Rekomendasi, Lihat Hasil Rekomendasi, Export Hasil ke Excel

### Class Diagrams (3 diagrams)
- **ORM Models** (fig3.3): 5 entities — Supervisor, AppUser, RecommendationRun, Recommendation, Student. Relationships: Supervisor 1→N Recommendation, AppUser 1→N RecommendationRun, RecommendationRun 1→N Recommendation, Recommendation N→1 Student
- **Runtime Dataclasses** (fig3.3b): RunOverrides, RecommendationOutput, CapacityPlan, RecommendationItem, SupervisorProfile (frozen), EmbeddingProvider (singleton), EmbeddingInfo
- **Service Architecture** (fig3.3c): RecommenderService (service) → uses RecommendationEngine (utility), ExcelIO (utility), QueryRepository (repository), Evaluator (utility). RecommendationEngine → uses EmbeddingProvider (singleton), DocumentBuilder (utility)

### ERD (fig3.4)
5 tables: `students`, `recommendations`, `recommendation_runs`, `supervisors`, `app_users`. No vector store — embeddings computed on-the-fly at inference time.

### Deployment Architecture (BAB 3 — Cloud VPS, not on-premises)
- **VPS**: Tencent Cloud, 2 vCPU, 8 GB RAM, 80 GB SSD, Ubuntu 22.04 LTS, 4 GB swap
- **Stack**: nginx (reverse proxy, SSL via Let's Encrypt) → gunicorn (1 worker, timeout 300s) → Flask app → SQLite
- **Containerization**: Docker + Docker Compose; Dockerfile from `python:3.11-slim`; Docker volumes for persistent DB + model cache
- **RAM budget**: ~5.5–6.0 GB for 2 models loaded simultaneously (bge-m3 + Qwen3, each ~2.0–2.5 GB)
- **Inference time**: 2–4 minutes on CPU (nginx timeout set to 360s)
- **Note**: BAB 2 describes "on-premises deployment" conceptually but BAB 3 actual deployment is cloud VPS — minor internal inconsistency

### Evaluation Strategy
- 18 experiments: 3 models × 3 toggle configs (extra_docs, group_bonus) × 2 capacity variants
- Ground truth: `current_supervisor_code` from historical EPC assignments (batch 2026 CS Program)
- 171 total students; 168 with valid ground truth (3 Study Abroad excluded from evaluation)
- IR metrics + Assignment Match Rate
- Fully deterministic/reproducible pipeline

**Assessment:** BAB 3 fully and rigorously answers RQ1. The pipeline design is systematic, the model comparison is fair, and the evaluation strategy is well-designed. The 18-experiment matrix with controlled variables is a strong methodological choice.

---

## 6. Findings (BAB 4) — Do Results Answer the Research Questions?

### Testing Environment
- **Hardware**: Apple M4 Pro (12-core CPU), 24 GB Unified Memory, Apple M4 Pro GPU (20-core) + Neural Engine (16-core), 1 TB SSD
- **Software**: macOS Sequoia 15, Python 3.12, Flask 3.1.0, SQLAlchemy 2.0.49 + SQLite, sentence-transformers, pandas/numpy/openpyxl, Visual Studio Code

### Model Comparison (Identical Config: extra_docs=True, group_bonus=False)

| Model | MRR | Hit@1 | Hit@5 | nDCG@5 | nDCG@10 | Avg Rank | Match Rate |
|---|---|---|---|---|---|---|---|
| **BAAI/bge-m3** (Run 26) | **0.711** | **0.560** | **0.929** | **0.759** | **0.779** | **2.17** | 0.530 |
| Qwen/Qwen3-Embedding-0.6B (Run 32) | 0.617 | 0.470 | 0.851 | 0.660 | 0.698 | 2.95 | 0.512 |
| intfloat/multilingual-e5-large-instruct (Run 38) | 0.672 | 0.506 | 0.905 | 0.721 | 0.748 | 2.43 | **0.583** |
| Random baseline | ~0.23 | ~0.07 | ~0.36 | ~0.21 | — | ~7.5 | — |

### Similarity Score Spread Analysis

| Model | Avg Similarity @1 | Avg True Similarity | Spread (selisih) |
|---|---|---|---|
| BAAI/bge-m3 | 0.674 | 0.645 | 0.030 |
| Qwen3-0.6B | 0.726 | 0.684 | 0.042 |
| mE5-large-instruct | 0.907 | 0.893 | **0.014** |

**Key insight on the mE5 inversion:** mE5-large achieves the best Assignment Match Rate (0.583) but lower retrieval metrics than bge-m3. The reason: mE5 produces very high but nearly uniform scores (avg@1 = 0.907, avg_true = 0.893, spread only 0.014). It rates almost all pairs as "very similar," losing discriminative power for ranking, which accidentally aligns better with capacity-driven slot distribution. BGE-M3 has sharper discrimination (spread 0.030) and correctly ranks semantically relevant supervisors higher.

**Correct conclusion drawn:** Retrieval metrics are the primary quality signal; Assignment Match Rate is complementary but can mislead when used alone.

### Parameter Sensitivity

**extra_docs effect:**
| Model | extra_docs=True | extra_docs=False | Δ%Rank-1 | Recommendation |
|---|---|---|---|---|
| BAAI/bge-m3 | 67.8% | 62.0% | +5.8pp | Enable |
| Qwen3-0.6B | 49.1% | 50.9% | -1.8pp | Disable |
| mE5-large | 54.4% | 60.8% | -6.4pp | Disable |

Model-dependent: extra docs help bge-m3 (richer profiles → better discrimination) but hurt mE5-large (disrupts already-calibrated embedding space).

**group_bonus effect:** Zero impact on retrieval metrics across all 3 models. When `extra_docs` is controlled, %Rank-1 is identical between `group_bonus=True` and `group_bonus=False`. The apparent differences in cross-batch comparisons are spurious (group_bonus=True was always paired with extra_docs=False — variables never independently varied). The bonus is technically applied (gap fin-sim ≈ 0.008) but not large enough to change rankings.

### Best Configuration (Run 26)
| Metric | Value |
|---|---|
| MRR | 0.711 |
| Hit@1 | 56.0% |
| Hit@5 | 92.9% |
| nDCG@10 | 0.779 |
| %Rank-1 | 67.8% |
| %Top-3 | 85.4% |
| Avg Rank | 2.17 |

### Load Distribution (addresses RQ3)
- All 18 configurations produce **identical distributions**
- Total assigned: 171 students (including 3 Study Abroad; evaluation uses 168)
- Min: 12 students/supervisor, Max: 13 students/supervisor
- Average: 12.21, StdDev: 0.426
- Gini Coefficient: **0.0138** (near-perfect equality)
- 3 supervisors with 13 students, 11 supervisors with 12 students

This is a major finding for RQ3 — the system solves the **load imbalance problem** completely, regardless of model choice.

### Assignment Distribution (addresses RQ3 - quality)
Distribution pooled from 3,078 assignment slots (18 configurations × 171 students):
| Assigned Rank | Count | Percentage |
|---|---|---|
| 1 | 1,779 | 57.8% |
| 2 | 411 | 13.4% |
| 3 | 313 | 10.2% |
| 4 | 147 | 4.8% |
| 5 | 76 | 2.5% |
| 6+ | 352 | 11.4% |

81.4% of students assigned within top-3. 11.4% beyond rank 5 due to capacity saturation (mathematical consequence of slot limits, not model failure).

### Web Application Pages (BAB 4 Section 4.2.5)
10 distinct UI views documented with screenshots:
1. **Login Page** — credential form
2. **Register Page** — account creation with full name, username, password, confirm password
3. **Dashboard** (Command Center) — statistics summary, quality trend chart, last run snapshot, Export Excel
4. **Data Center** — ETL hub; Excel upload/import; pipeline trigger button
5. **Generate New Run** (modal) — model selection, extra_docs toggle, group_bonus toggle, capacity min/max
6. **Run History** — table of up to 250 runs (descending order), with Details/Export per row
7. **Run Detail** — metrics (MRR, Hit@1, Hit@5, NDCG@5, NDCG@10, Objective Score), capacity per supervisor (color-coded), mismatch spotlight (top-10 mismatches)
8. **Run Detail metrics** (continuation of Run Detail page, separate figure)
9. **Run Recommendations** — full assignment table with NIM, Name, Track, Partner, Topic, Current Supervisor, Recommended Supervisor, Final Score, Rule Match; server-side filter; mismatch highlight
10. **Supervisor Studio** — searchable dropdown, Load Supervisor, Export Config Excel, Keywords Studio (chip-based), Add Supervisor form, Supervisor Table
11. **Logout** — POST to /logout, session cleared

BAB 5 formally claims **"delapan halaman fungsional"** (8 functional pages): Login, Register, Dashboard, Data Center, Generate New Run, Run History, Run Detail, Supervisor Studio. (Logout = function, not page; Run Recommendations = tab within Run Detail.)

### Black Box Testing (BAB 4 Section 4.3 — 9 test sections, 56 total scenarios, all Pass)
| Section | Scenarios | Pass |
|---|---|---|
| Login Page | 3 | ✅ |
| Register Page | 5 | ✅ |
| Dashboard | 10 | ✅ |
| Data Center | 7 | ✅ |
| Generate New Run | 8 | ✅ |
| Run History | 5 | ✅ |
| Supervisor Studio | 9 | ✅ |
| Run Detail | 7 | ✅ |
| Logout | 2 | ✅ |
| **Total** | **56** | **All Pass** |

Method: Equivalence Partitioning (valid + invalid input categories).

**Assessment:** BAB 4 directly and quantitatively answers RQ1 and RQ2. RQ3 (efficiency) is answered through the load distribution analysis and automation evidence, though time savings are not empirically measured.

---

## 7. Conclusion Analysis (BAB 5) — Does It Close the Loop?

### Conclusion vs Research Questions

**Simpulan 1 — Current process problems (context for RQ1):**
> Manual EPC process takes 1–3 weeks, relies on individual knowledge, no structured matching mechanism, uneven load distribution, relevance not guaranteed.
✅ **Sets up the motivation.**

**Simpulan 2 — System design and development (RQ1):**
> Scoring approach combining (a) text embedding semantic similarity and (b) company group bonus; greedy solver with capacity constraints; web app with 8 functional pages; all black box test scenarios Pass.
✅ **Fully answered.**

**Simpulan 3 — Match rate with ground truth (RQ2):**
> BAAI/bge-m3 with extra_docs=True: MRR=0.711, Hit@5=0.929, avg_rank=2.17 — far above random baseline (MRR~0.23). Assignment Match Rate 0.530 indicates adequate alignment with historical ground truth.
✅ **Answered with precision.**

**Simpulan 4 — Efficiency improvement (RQ3):**
> "Sistem yang dikembangkan berpotensi meningkatkan efisiensi proses pemetaan melalui otomasi perhitungan kemiripan dan pembangkitan rekomendasi yang sebelumnya dilakukan secara manual."
⚠️ **Partially answered — qualitative only.** The word "berpotensi" (potentially) is honest but the 1–3 week manual benchmark is never compared against measured system runtime (2–4 minutes on CPU). This remains the **weakest closure** in the thesis.

### Future Work Proposals (7 items)
1. **Expand supervisor data sources** — publications, research portfolios (directly addresses BAB 4 extra_docs limitations)
2. **Expand to other enrichment tracks** — research, entrepreneurship, professional development
3. **Fine-tune embedding models** on Indonesian academic domain data (addresses zero-shot limitation)
4. **SUS evaluation** with real EPC users (fills the missing usability evaluation gap)
5. **Integration with institutional academic information system** (reduces manual Excel upload)
6. **Longitudinal study** tracking recommendation quality across multiple mapping periods (addresses single-batch ground truth limitation)
7. **Replication to other faculties** as generalizability test — 168 students and 14 supervisors from one CS program batch; test whether embedding models and greedy solver behave consistently for other faculties, scales, and data characteristics

---

## 8. Critical Assessment

### Strengths

1. **Rigorous controlled experiment design** — 18 experiments with single-variable isolation allows clean attribution of performance differences to model choice vs. configuration.
2. **Honest metric selection** — the thesis explicitly warns that Assignment Match Rate alone can mislead; retrieval metrics are correctly prioritized.
3. **Strong quantitative results** — MRR 0.711 and Hit@5 0.929 are genuinely impressive for a zero-shot system with no domain fine-tuning.
4. **Load balance solved completely** — Gini 0.0138 is near-perfect; this directly solves one of the stated pain points from BAB 1.
5. **Single-document supervisor representation correctly described** — post-mismatch-fixes, BAB 3 now accurately states one profile document per supervisor (no max-pooling).
6. **Production-quality prototype** — 10 UI views, proper auth, export in 2 Excel formats, server-side filtering, cloud deployment with nginx+gunicorn+Docker.
7. **Transparent negative result** — group_bonus has zero effect on rankings; the thesis is honest about this.

### Weaknesses / Gaps

1. **RQ3 efficiency gap** — The claim that the system "improves efficiency" is not empirically validated with time measurements. The 1–3 week manual benchmark from BAB 1 is never revisited with a measured automated equivalent. System runtime (2–4 minutes on CPU) is documented in BAB 3 deployment section but never framed as a comparison against the manual baseline in BAB 5.

2. **Ground truth quality limitation** — Historical EPC assignments were sometimes made "randomly without considering expertise alignment" (per BAB 1). Using this as ground truth means Match Rate of 0.530 may not reflect true semantic alignment. This limitation is acknowledged implicitly (retrieval metrics prioritized over match rate) but not explicitly in the conclusion.

3. **group_bonus had zero effect** — The company group bonus parameter produces no measurable improvement in retrieval quality (delta ~0.008, insufficient to change rankings). The feature exists in the codebase adding complexity without measured value.

4. **No actual user study** — All black box testing was functional correctness testing by the researcher. No EPC end-user evaluation of usability or acceptance. Acknowledged in future work (SUS evaluation) but represents a gap between system prototype and validated tool.

5. **Single dataset / single institution** — 168 students and 14 supervisors from one XYZ CS faculty, one batch. Generalizability untested. Future work item 7 explicitly addresses this.

6. **mE5-large chosen as default but performs 2nd** — BAB 2 (line 163) states mE5-large-instruct is the "default model" due to consistent performance on mixed Indonesian-English text. BAB 4 evidence shows bge-m3 is clearly superior on all retrieval metrics. The final system correctly uses bge-m3 as the recommended choice (BAB 4 conclusion), but the BAB 2 statement is a pre-experiment assumption that contradicts the experimental conclusion.

7. **BAB 2 "on-premises" vs BAB 3 cloud VPS** — BAB 2's "On-Premises Deployment" section describes the system as deployed "secara lokal menggunakan Docker container" but BAB 3 implementation section deploys on Tencent Cloud VPS (2 vCPU, 8GB RAM) with nginx+gunicorn. Minor internal inconsistency.

---

## 9. Research Questions vs. Evidence Traceability

| Research Question | BAB 3 Addresses? | BAB 4 Evidence | BAB 5 Closes? | Strength |
|---|---|---|---|---|
| RQ1: Design & develop semantic similarity system | ✅ Full pipeline design, 3 models, 12 UCs, 8 ADs, 3 class diagrams, ERD, deployment | ✅ 18 experiments, 56 black box scenarios all Pass | ✅ Confirmed with architecture summary | Strong |
| RQ2: Match rate vs. ground truth | ✅ Evaluation strategy, 7 metrics defined | ✅ MRR=0.711, Hit@5=0.929, MatchRate=0.530 | ✅ Numbers cited directly | Strong |
| RQ3: Efficiency improvement | ⚠️ Automated pipeline described; runtime 2–4 min on CPU noted; no baseline time measurement | ⚠️ Load distribution (Gini 0.0138) + automation demonstrated; no time comparison | ⚠️ "Berpotensi" (potential) only, not measured | Moderate |

---

## 10. Summary Verdict

This is a **well-executed applied research thesis** with a clear problem, a systematic methodology, honest experimental design, and strong quantitative results. The system works: bge-m3 places the correct supervisor in the top-5 for 92.9% of students, the solver achieves near-perfect load balance (Gini 0.0138), and the web application passes all 56 functional tests across 9 test sections.

The primary gap is in RQ3 — efficiency improvement is demonstrated architecturally and functionally but never measured empirically with real timing data. System runtime (2–4 minutes on CPU) vs. 1–3 weeks manually is the obvious comparison, but this is not drawn in BAB 5. This is a common limitation in software prototype research and acceptable at the undergraduate thesis level.

The experimental finding that `group_bonus` has no effect on rankings is intellectually honest. The discovery that `extra_docs` helps bge-m3 but hurts mE5-large is a substantive contribution demonstrating model-dependent sensitivity to profile enrichment.

Overall: the thesis closes both RQ1 and RQ2 convincingly. RQ3 is closed with a reasonable but unmeasured argument. The conclusions in BAB 5 are accurate, grounded in BAB 4 evidence, and the 7 future work proposals are genuinely useful.

---

## 11. BAB 3 Implementation Mismatches — Resolved (2026-06-14)

Four mismatches found between original bab3.tex methodology claims and the actual implementation in `feature-system-en.md`. All resolved.

### Mismatch 1 — Multi-document supervisor representation (SIGNIFICANT)
**Claim:** Each supervisor had multiple documents per source type; similarity aggregated via max-pooling across documents.
**Reality:** `profile_document(profile)` in `rules.py` produces **one document per supervisor** (keywords + labels joined). When `extra_docs=True`, historical data is merged **into the same profile**, not stored as separate documents. Max-pooling never happens.
**Fixed in:** Lines 181 (`Konsep Dasar`), 187 (`Pembentukan Representasi`), 298–300 (`Mekanisme Perhitungan`). Current bab3 confirms single-profile + direct N×M matrix.

### Mismatch 2 — Student document fields (SIGNIFICANT)
**Claim:** Student text used `posisi kerja + peran + nama perusahaan`. Table showed `Perusahaan=Ya` and `Kategori perusahaan=Ya`.
**Reality:** `student_document` = `track + position_topic + work_schema`. `partner_lecturer` (company name) is **not** in the student embedding text — it is used only for company group bonus grouping.
**Fixed in:** Line 220 paragraph and Table "Pemrosesan Data Mahasiswa" — now accurately shows track/position_topic/work_schema as text fields, partner_lecturer as "bonus only" (not text).

### Mismatch 3 — Residual "tersimpan dalam knowledge base" (MINOR)
**Claim:** Embeddings were "stored in a knowledge base" (line 296).
**Reality:** Embeddings are computed at runtime into an in-memory N×M matrix; nothing is persisted.
**Fixed in:** Line 296 — rewritten to "dihitung pada saat inferensi."

### Mismatch 4 — L2 normalization claim (MINOR)
**Claim:** "Embedding digunakan tanpa normalisasi manual tambahan."
**Reality:** `embedding.py` explicitly applies L2 normalization; cosine similarity is computed as dot product on normalized vectors.
**Fixed in:** Line 268 — now states L2 normalization is applied by `embedding.py`.
