# Thesis Report: Comprehensive Analysis
**Sistem Rekomendasi Faculty Supervisor Berbasis Semantic Similarity**
**XYZ University — Enrichment Program**

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

The progression from TF-IDF to transformer embedding is well-motivated: TF-IDF misses semantic equivalence when students and supervisors use different terminology for the same domain.

### Evaluation Metrics Grounded
- **MRR** — measures how fast the correct supervisor appears in ranked list
- **Hit@1 / Hit@5** — binary hit within top-K
- **nDCG@5 / nDCG@10** — position-weighted ranking quality
- **Precision@K** — proportion of relevant in top-K
- **Recall@K** — coverage of relevant results
- **Assignment Match Rate** — end-to-end match against historical assignments

### Related Research (6 Studies)
| Paper | Method | Relevance |
|---|---|---|
| Zhang et al. (2016) | Personality matching | Personality-aware supervisor rec |
| Cohan et al. (2020) — SPECTER | BERT + Citation Graph | Scientific doc similarity for reviewer matching |
| Nagarajan et al. (2025) | TF-IDF + BERT Hybrid | Academic collaboration rec |
| Wang et al. (2025) — AVRD | Chinese BERT + SimCSE | Advisor recommendation via text |
| Sabilillah et al. (2024) | BERT + Cosine Similarity | Thesis supervisor rec |
| Dasri et al. (2025) | MapReduce K-Skyband | Two-way thesis supervisor rec |

**Gap identified:** None of the prior work uses the newest generation of zero-shot multilingual dense retrieval models (BGE-M3, mE5-large-instruct, Qwen3-Embedding) comparatively for a bilingual (Indonesian–English) academic supervisor recommendation context, nor integrates quota constraints into a web DSS.

---

## 5. Methodology (BAB 3) — Does It Answer the Research Questions?

### Research Framework
The thinking framework (Kerangka Berpikir) flows logically:
```
Problem Analysis → Planning → Recommendation Model Design →
Evaluation (meet criteria?) → Yes: UML Design → Web Prototype
                           → No: back to Planning
```

### Data Sources
**Student data (text input):**
- Job position, role, company name, company category
- Combined into a single text string for embedding

**Supervisor data (multi-document representation):**
- Historical mentoring data (position, company, job description of previously mentored students)
- Thesis recommendation topics (title, description, problem statement, expected output, thesis type)
- Each supervisor has 1–N documents depending on data availability

### 8-Stage Pipeline (addresses RQ1 comprehensively)
1. **Excel Import** — EPC uploads student data via web UI; upsert to DB
2. **Text Document Construction** — student text = position+role+company; supervisor docs = keywords + optional historical/thesis data
3. **Embedding + Similarity** — SentenceTransformer (primary) → TF-IDF fallback → Token Overlap fallback; cosine similarity N×M matrix
4. **Hybrid Scoring** — similarity × weight + optional company group bonus
5. **Capacity Planning** — min/max slots per supervisor; overflow/underflow handled
6. **Greedy Solver** — 2-phase: initialize (argmax), phase 1 (reduce overfull), phase 2 (fill underfull)
7. **Evaluation** — metrics computed at 3 levels: content-based, hybrid, post-assignment
8. **Save & Export** — DB storage + Excel export (standard 3-sheet / detailed 4-sheet)

### 3 Candidate Models
| Model | Context Window | Parameters | Key Strength |
|---|---|---|---|
| BAAI/bge-m3 | 8,192 tokens | ~570M | Multi-retrieval (dense+sparse+multi-vector), 100+ languages |
| Qwen3-Embedding-0.6B | 32,000 tokens | ~600M | Longest context, lightest model, MRL support |
| intfloat/multilingual-e5-large-instruct | 512 tokens | ~560M | Instruction-following, default system model |

All models used zero-shot (no fine-tuning) on the same pipeline, ensuring fair comparison.

### Evaluation Strategy
- 18 experiments: 3 models × 3 toggle configs (extra_docs, group_bonus) × 2 capacity variants
- Ground truth: `current_supervisor_code` from historical EPC assignments
- IR metrics + Assignment Match Rate
- Deterministic/reproducible pipeline

**Assessment:** BAB 3 fully and rigorously answers RQ1. The pipeline design is systematic, the model comparison is fair, and the evaluation strategy is well-designed. The 18-experiment matrix with controlled variables is a strong methodological choice.

---

## 6. Findings (BAB 4) — Do Results Answer the Research Questions?

### Model Comparison (Identical Config: extra_docs=True, group_bonus=False)

| Model | MRR | Hit@1 | Hit@5 | nDCG@5 | nDCG@10 | Avg Rank | Match Rate |
|---|---|---|---|---|---|---|---|
| **BAAI/bge-m3** | **0.711** | **0.560** | **0.929** | **0.759** | **0.779** | **2.17** | 0.530 |
| Qwen3-Embedding-0.6B | 0.617 | 0.470 | 0.851 | 0.660 | 0.698 | 2.95 | 0.512 |
| mE5-large-instruct | 0.672 | 0.506 | 0.905 | 0.721 | 0.748 | 2.43 | **0.583** |
| Random baseline | ~0.23 | ~0.07 | ~0.36 | ~0.21 | — | ~7.5 | — |

**Key insight on the mE5 inversion:** mE5-large achieves the best Assignment Match Rate (0.583) but lower retrieval metrics than bge-m3. The reason: mE5 produces very high but nearly uniform scores (avg@1 = 0.907, avg_true = 0.893, spread only 0.014). It rates almost all pairs as "very similar," losing discriminative power for ranking, which accidentally aligns better with capacity-driven slot distribution. BGE-M3 has sharper discrimination (spread 0.030) and correctly ranks semantically relevant supervisors higher.

**Correct conclusion drawn:** Retrieval metrics are the primary quality signal; Assignment Match Rate is complementary but can mislead when used alone.

### Parameter Sensitivity

**extra_docs effect:**
| Model | extra_docs=True | extra_docs=False | Δ%Rank-1 | Recommendation |
|---|---|---|---|---|
| BAAI/bge-m3 | 67.8% | 62.0% | +5.8pp | Enable |
| Qwen3-0.6B | 49.1% | 50.9% | -1.8pp | Disable |
| mE5-large | 54.4% | 60.8% | -6.4pp | Disable |

Model-dependent: extra docs help bge-m3 (richer sparse profiles → better discrimination) but hurt mE5-large (disrupts already-calibrated embedding space).

**group_bonus effect:** Zero impact on retrieval metrics across all 3 models. The gap (fin-sim ≈ 0.008) is technically applied but not large enough to change rankings. This is an honest finding — the feature works mechanically but doesn't materially improve quality.

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
- Min: 12 students/supervisor, Max: 13 students/supervisor
- Gini Coefficient: **0.0138** (near-perfect equality)
- Standard deviation: 0.426

This is a major finding for RQ3 — the system solves the **load imbalance problem** completely, regardless of model choice.

### Assignment Distribution (addresses RQ3 - quality)
- 57.8% of students assigned to their rank-1 supervisor
- 81.4% assigned within top-3
- Only 11.4% assigned beyond rank 5 (due to capacity saturation, not model failure)

### Black Box Testing (8 pages, all Pass)
- Login, Register, Dashboard, Data Center, Generate New Run, Run History, Supervisor Studio, Run Detail, Logout
- All 50+ scenarios pass (equivalence partitioning method)

**Assessment:** BAB 4 directly and quantitatively answers RQ1 and RQ2. RQ3 (efficiency) is answered through the load distribution analysis and automation evidence, though time savings are not empirically measured.

---

## 7. Conclusion Analysis (BAB 5) — Does It Close the Loop?

### Conclusion vs Research Questions

**RQ1 — System design and development:**
> "Sistem rekomendasi yang dikembangkan menggunakan pendekatan scoring yang menggabungkan dua komponen: (a) kemiripan semantik berbasis text embedding... dan (b) company group bonus... diimplementasikan sebagai aplikasi berbasis web yang mencakup delapan halaman fungsional... seluruh skenario pengujian menghasilkan status Pass."

✅ **Fully answered.** The conclusion accurately summarizes the architecture and validates it with black box testing.

**RQ2 — Match rate with ground truth:**
> "Model terbaik, yaitu BAAI/bge-m3... mencapai nilai MRR sebesar 0,711, Hit@5 sebesar 0,929, dan rata-rata peringkat 2,17 --- jauh di atas random baseline (MRR ~0,23). Assignment Match Rate sebesar 0,530..."

✅ **Answered with precision.** Numbers are specific, contextualized against baseline, and the MRR/Hit@5 framing is appropriate (ground truth supervisor appears in top-5 for 92.9% of students).

**RQ3 — Efficiency improvement:**
> "Sistem yang dikembangkan berpotensi meningkatkan efisiensi proses pemetaan melalui otomasi perhitungan kemiripan dan pembangkitan rekomendasi yang sebelumnya dilakukan secara manual."

⚠️ **Partially answered — qualitative only.** The conclusion correctly states the *potential* for efficiency improvement but does not provide measured time savings (before vs. after). The problem statement mentions 1–3 weeks per batch manually; the system automates this to near-instant generation, but this comparison is not quantified in the conclusion. This is the **weakest closure** in the thesis.

### Future Work Proposals (6 items)
1. Expand supervisor data sources (publications, research portfolios)
2. Expand to other enrichment tracks (research, entrepreneurship, professional development)
3. Fine-tune embedding models on Indonesian academic domain data
4. SUS (System Usability Scale) evaluation with real EPC users
5. Direct integration with institutional academic information system
6. Longitudinal study tracking recommendation quality across multiple mapping periods

These are well-grounded — items 1 and 3 directly address limitations discovered in BAB 4, item 4 fills the missing usability evaluation gap, items 2 and 5 are natural scope expansions, and item 6 addresses the ground truth quality concern.

---

## 8. Critical Assessment

### Strengths

1. **Rigorous controlled experiment design** — 18 experiments with single-variable isolation allows clean attribution of performance differences to model choice vs. configuration.
2. **Honest metric selection** — the thesis explicitly warns that Assignment Match Rate alone can mislead; retrieval metrics are correctly prioritized.
3. **Strong quantitative results** — MRR 0.711 and Hit@5 0.929 are genuinely impressive for a zero-shot system with no domain fine-tuning.
4. **Load balance solved completely** — Gini 0.0138 is near-perfect; this directly solves one of the stated pain points from BAB 1.
5. **Multi-document supervisor representation** — using both historical data and thesis recommendations as separate documents is a thoughtful design that enriches sparse supervisor profiles.
6. **Production-quality prototype** — 8 functional pages, proper auth, export in 2 Excel formats, server-side filtering — not a toy demo.

### Weaknesses / Gaps

1. **RQ3 efficiency gap** — The claim that the system "improves efficiency" is not empirically validated with time measurements. The 1–3 week manual benchmark from BAB 1 is never revisited with a measured automated equivalent. A simple estimation ("system generates assignments in X seconds vs. X weeks manually") would close this gap.

2. **Ground truth quality limitation** — Historical EPC assignments were sometimes made "randomly without considering expertise alignment" (per BAB 1). Using this as ground truth means Match Rate of 0.530 may not reflect true semantic alignment — it reflects historical decisions that were themselves acknowledged to be sub-optimal. This limitation is acknowledged implicitly (the thesis argues retrieval metrics are more meaningful) but not addressed explicitly in the conclusion.

3. **group_bonus had zero effect** — The company group bonus parameter, which was a designed feature, produced no measurable improvement in retrieval quality. The thesis is transparent about this (the parameter is technically applied but the delta is ~0.008, insufficient to change rankings), but the feature exists in the codebase adding complexity without measured value.

4. **No actual user study** — All black box testing was functional correctness testing by the researcher. There is no EPC end-user evaluation of usability or acceptance. This is acknowledged in the future work (SUS evaluation) but represents a gap between system prototype and validated tool.

5. **Single dataset / single institution** — The evaluation uses 168 students and 14 supervisors from one XYZ faculty. Generalizability to other faculties, batch sizes, or institutions is untested.

6. **mE5-large chosen as default but performs 2nd** — The thesis mentions mE5-large was set as the "default model" in the system (BAB 2 subsection), but the experimental results show bge-m3 is clearly superior on retrieval metrics. The final system should use bge-m3 as default (which BAB 4 correctly concludes), but the BAB 2 statement should be noted as a pre-experiment assumption that was later corrected by evidence.

---

## 9. Research Questions vs. Evidence Traceability

| Research Question | BAB 3 Addresses? | BAB 4 Evidence | BAB 5 Closes? | Strength |
|---|---|---|---|---|
| RQ1: Design & develop semantic similarity system | ✅ Full pipeline design, 3 models, UML | ✅ 18 experiments, all functional tests pass | ✅ Confirmed | Strong |
| RQ2: Match rate vs. ground truth | ✅ Evaluation strategy, 7 metrics defined | ✅ MRR=0.711, Hit@5=0.929, MatchRate=0.530 | ✅ Numbers cited directly | Strong |
| RQ3: Efficiency improvement | ⚠️ Automated pipeline described but no baseline time measurement | ⚠️ Load distribution + automation demonstrated; no time comparison | ⚠️ "Potential" only, not measured | Moderate |

---

## 10. Summary Verdict

This is a **well-executed applied research thesis** with a clear problem, a systematic methodology, honest experimental design, and strong quantitative results. The system works: bge-m3 places the correct supervisor in the top-5 for 92.9% of students, the solver achieves near-perfect load balance, and the web application passes all functional tests.

The primary gap is in RQ3 — efficiency improvement is demonstrated architecturally and functionally but never measured empirically with real timing data. This is a common limitation in software prototype research and is acceptable at the undergraduate thesis level, though a simple timing comparison would strengthen the argument significantly.

The experimental finding that `group_bonus` has no effect on rankings is intellectually honest and worth highlighting as a negative result — it narrows the design space for future work. The discovery that `extra_docs` helps bge-m3 but hurts mE5-large is a substantive contribution that demonstrates model-dependent sensitivity to profile enrichment strategies.

Overall: the thesis closes both RQ1 and RQ2 convincingly. RQ3 is closed with a reasonable but unmeasured argument. The conclusions in BAB 5 are accurate, grounded in BAB 4 evidence, and the future work proposals are genuinely useful rather than boilerplate.

---

## 11. BAB 3 Implementation Mismatches — Resolved (2026-06-14)

Four mismatches found between bab3.tex methodology claims and the actual implementation in `feature-system-en.md`. All resolved.

### Mismatch 1 — Multi-document supervisor representation (SIGNIFICANT)
**Claim:** Each supervisor had multiple documents per source type; similarity aggregated via max-pooling across documents.
**Reality:** `profile_document(profile)` in `rules.py` produces **one document per supervisor** (keywords + labels joined). When `extra_docs=True`, historical data is merged **into the same profile**, not stored as separate documents. Max-pooling never happens.
**Fixed in:** Lines 181 (`Konsep Dasar`), 187 (`Pembentukan Representasi`), 298–300 (`Mekanisme Perhitungan`). Replaced multi-doc / aggregation language with single-profile + direct N×M matrix language.

### Mismatch 2 — Student document fields (SIGNIFICANT)
**Claim:** Student text used `posisi kerja + peran + nama perusahaan`. Table showed `Perusahaan=Ya` and `Kategori perusahaan=Ya`.
**Reality:** `student_document` = `track + position_topic + work_schema`. `partner_lecturer` (company name) is **not** in the student embedding text — it is used only for company group bonus grouping. There is no "kategori perusahaan" field; `track` is the study program.
**Fixed in:** Line 220 paragraph and Table "Pemrosesan Data Mahasiswa" — rows now show `track(Ya)`, `position_topic(Ya)`, `work_schema(Ya)`, `partner_lecturer(Tidak, bonus only)`.

### Mismatch 3 — Residual "tersimpan dalam knowledge base" (MINOR)
**Claim:** Embeddings were "stored in a knowledge base" (line 296).
**Reality:** Embeddings are computed at runtime into an in-memory N×M matrix; nothing is persisted.
**Fixed in:** Line 296 — rewritten to "dihitung pada saat inferensi."

### Mismatch 4 — L2 normalization claim (MINOR)
**Claim:** "Embedding digunakan tanpa normalisasi manual tambahan."
**Reality:** `embedding.py` explicitly applies L2 normalization; cosine similarity is computed as dot product on normalized vectors.
**Fixed in:** Line 268 — now states L2 normalization is applied by `embedding.py`.
