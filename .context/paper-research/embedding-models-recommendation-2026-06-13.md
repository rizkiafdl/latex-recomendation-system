# Papers Using BGE-M3 / E5 / Qwen / SBERT for Recommendation Systems
> Generated: 2026-06-13 | 3-agent discovery swarm + 5-agent parallel enrichment
> Goal: Find papers using the same embedding models as thesis (BGE-M3, E5-multilingual, Qwen3-Embedding, SBERT) for recommendation/matching, to map the research gap.

---

## Thesis Context

This thesis uses **BGE-M3 + E5-multilingual-large-instruct + Qwen3-Embedding-0.6B** (multilingual dense retrieval) for faculty supervisor recommendation at BINUS University — matching student thesis proposals to supervisor research profiles via cosine similarity.

Current Penelitian Terkait table already has:
- `cohan2020specter` — SPECTER/ACL 2020 (BERT+citation graph for scientific paper matching)
- `wang2025avrd` — Frontiers 2025 (Chinese BERT + SimCSE for advisor recommendation)
- `nagarajan2025bert` — Springer 2025 (BERT hybrid for academic collaboration rec)
- `sabilillah2024bert` — Edumatic 2024 (BERT + cosine similarity, Indonesian)
- `zhang2016supervisor` — HICSS 2016 (personality-based supervisor matching)
- `dasri2025twoway` — JOIV 2025 (MapReduce K-Skyband, two-way rec)

---

## Verified Papers — Embedding Models for Recommendation/Matching

---

### 1. Deep Learning for Journal Recommendation System of Research Papers
| Field | Value |
|---|---|
| **Authors** | Gündoğan, Esra; Kaya, Mehmet; Daud, Ali |
| **Year** | 2023 (online Oct 2022) |
| **Journal** | Scientometrics, Vol. 128(1), pp. 461–481 |
| **DOI** | `10.1007/s11192-022-04535-y` ✅ verified |
| **Citations** | 16 (OpenAlex) |
| **Open Access** | ❌ Springer paywall |
| **Models used** | **SBERT** vs Word2Vec, GloVe, FastText |

**Abstract:** Uses Sentence-BERT to compute semantic similarity between research article content (title, abstract, keywords) and journal scope descriptions for recommending the appropriate journal for manuscript submission. SBERT outperforms all word-embedding baselines.

**Relevance to thesis:** Direct analog — uses SBERT to match text content to a target profile (journal scope), exactly as this thesis matches student proposals to supervisor profiles. Shows SBERT is the current baseline for embedding-based academic recommendation. This thesis advances beyond this by using BGE-M3/E5/Qwen3 (stronger multilingual embeddings).

**BibTeX:**
```bibtex
@article{gundogan2023journal,
  author    = {Gündoğan, Esra and Kaya, Mehmet and Daud, Ali},
  title     = {Deep learning for journal recommendation system of research papers},
  journal   = {Scientometrics},
  volume    = {128},
  number    = {1},
  pages     = {461--481},
  year      = {2023},
  doi       = {10.1007/s11192-022-04535-y},
  url       = {https://doi.org/10.1007/s11192-022-04535-y}
}
```

---

### 2. CareerBERT: Matching Resumes to ESCO Jobs in a Shared Embedding Space for Generic Job Recommendations
| Field | Value |
|---|---|
| **Authors** | Rosenberger, Julian; Wolfrum, Lukas; Weinzierl, Sven; Kraus, Mathias; Zschech, Patrick |
| **Year** | 2025 |
| **Journal** | Expert Systems with Applications (Elsevier), Vol. 275, Article 127043 |
| **DOI** | `10.1016/j.eswa.2025.127043` ✅ verified |
| **arXiv** | arXiv:2503.02056 |
| **Citations** | 20 (OpenAlex) |
| **Open Access** | ✅ CC BY 4.0 |
| **PDF** | https://arxiv.org/pdf/2503.02056v1 |
| **Models used** | **BERT-based fine-tuned shared embedding space** |

**Abstract:** CareerBERT creates a unified embedding space from ESCO occupational taxonomy and EURES job listings to match resume text to job roles for recommendation. Evaluated with automated testing on real postings and expert HR assessment. Outperforms conventional and advanced embedding baselines.

**Relevance to thesis:** Resume-to-job matching via shared embedding space is structurally identical to proposal-to-supervisor matching. This paper uses fine-tuned BERT, whereas thesis uses pre-trained multilingual models (BGE-M3/E5/Qwen) without domain-specific fine-tuning — a key methodological distinction.

**BibTeX:**
```bibtex
@article{rosenberger2025careerbert,
  author    = {Rosenberger, Julian and Wolfrum, Lukas and Weinzierl, Sven and Kraus, Mathias and Zschech, Patrick},
  title     = {{CareerBERT}: Matching Resumes to {ESCO} Jobs in a Shared Embedding Space for Generic Job Recommendations},
  journal   = {Expert Systems with Applications},
  volume    = {275},
  pages     = {127043},
  year      = {2025},
  publisher = {Elsevier},
  doi       = {10.1016/j.eswa.2025.127043},
  url       = {https://doi.org/10.1016/j.eswa.2025.127043}
}
```

---

### 3. Chain-of-Factors Paper-Reviewer Matching
| Field | Value |
|---|---|
| **Authors** | Zhang, Yu; Shen, Yanzhen; Kang, SeongKu; Chen, Xiusi; Jin, Bowen; Han, Jiawei |
| **Year** | 2025 (submitted Oct 2023, accepted WWW 2025) |
| **Venue** | The Web Conference (WWW 2025) — peer reviewed |
| **DOI** | `10.48550/arXiv.2310.14483` |
| **arXiv** | arXiv:2310.14483 |
| **Citations** | 0 (very recent) |
| **Open Access** | ✅ arXiv preprint |
| **PDF** | https://arxiv.org/pdf/2310.14483v4 |
| **Models used** | Transformer-based semantic + topic + citation chain factors |

**Abstract (excerpt):** "With the rapid increase in paper submissions to academic conferences, the need for automated and accurate paper-reviewer matching is more critical than ever. Previous efforts have considered semantic similarity, shared topics, and citation connections between the paper and the reviewer's previous works."

**Relevance to thesis:** Paper-reviewer matching is the closest academic proxy to supervisor recommendation — both match a submitted document to an expert with a publication profile. Uses multi-factor semantic matching but not the latest multilingual dense models (BGE/E5/Qwen) — another gap this thesis fills.

**BibTeX:**
```bibtex
@inproceedings{zhang2025chainoffactors,
  author    = {Zhang, Yu and Shen, Yanzhen and Kang, SeongKu and Chen, Xiusi and Jin, Bowen and Han, Jiawei},
  title     = {Chain-of-Factors Paper-Reviewer Matching},
  booktitle = {Proceedings of the ACM Web Conference 2025 (WWW 2025)},
  year      = {2025},
  doi       = {10.48550/arXiv.2310.14483},
  eprint    = {2310.14483},
  archivePrefix = {arXiv},
  url       = {https://arxiv.org/abs/2310.14483}
}
```

---

### 4. Comparative Analysis of Sentence Transformers for Reference Paper Collection in Five Academic Fields
| Field | Value |
|---|---|
| **Authors** | Fahrudin, Tresna M.; Funabiki, Nobuo; Brata, Komang C.; Noprianto; Muhaimin, Amri; Hindrayani, Kartika M. |
| **Year** | 2025 |
| **Venue** | ACM CIIS 2025 (8th Int'l Conf on Computational Intelligence and Intelligent Systems), pp. 139–144 |
| **DOI** | `10.1145/3787256.3787277` ✅ verified |
| **Citations** | 0 (very recent) |
| **Open Access** | ✅ (per OpenAlex) |
| **PDF** | https://dl.acm.org/doi/pdf/10.1145/3787256.3787277 |
| **Models compared** | all-MiniLM-L6-v1, all-DistilRoBERTa-v1, all-mpnet-base-v2, SciBERT, SPECTER |

**Abstract (full):** Compares sentence transformer models for a reference paper collection system that accelerates academic literature search via multiple database APIs, semantic sentence embedding, and web scraping. Evaluates precision@10, NDCG@10, accessibility@10, and response time. Results show some models excel in all fields while others are domain-specific; SPECTER performs best in scientific domains.

**Relevance to thesis:** Directly compares multiple sentence transformer models for academic paper retrieval — the same model family used in this thesis. However, it only tests older/lighter models (MiniLM, SPECTER) and does NOT include BGE-M3, E5-multilingual-large-instruct, or Qwen3-Embedding. This is an explicit methodological gap this thesis fills for the Indonesian academic context.

**BibTeX:**
```bibtex
@inproceedings{fahrudin2025sentencetransformers,
  author    = {Fahrudin, Tresna Maulana and Funabiki, Nobuo and Brata, Komang Candra and Noprianto, Noprianto and Muhaimin, Amri and Hindrayani, Kartika Maulida},
  title     = {Comparative Analysis of Sentence Transformers for Reference Paper Collection in Five Academic Fields},
  booktitle = {Proceedings of the 2025 8th International Conference on Computational Intelligence and Intelligent Systems (CIIS 2025)},
  pages     = {139--144},
  year      = {2025},
  publisher = {ACM},
  doi       = {10.1145/3787256.3787277},
  url       = {https://dl.acm.org/doi/10.1145/3787256.3787277}
}
```

---

## Excluded / Not Recommended

| Paper | Reason excluded |
|---|---|
| Multi-BERT for Recommendation (arXiv:2308.13050) | arXiv preprint only, 2 citations, not peer-reviewed |
| ViRanker (arXiv:2509.09131) | BGE-M3 for reranking, not recommendation system |
| POLYRAG (arXiv:2504.14917) | BGE-M3 for medical document retrieval, not recommendation |
| JobFormer (arXiv:2404.04313) | Recommendation but not using BGE/E5/Qwen |
| Instruction-aware User Embedding (arXiv:2510.11016) | User embedding for commercial rec (Netflix/Amazon style), too different |
| Papers using RAG/QA (arXiv:2511.10297, 2512.12694, etc.) | RAG/QA application, not recommendation system |

---

## Research Gap Analysis

### What existing work does:
| Prior work | Model | Domain | Language |
|---|---|---|---|
| Cohan 2020 (SPECTER) | BERT + citation graph | Scientific paper embedding | English |
| Gündoğan 2023 | SBERT | Journal recommendation | English |
| Wang 2025 (AVRD) | Chinese BERT + SimCSE | Advisor recommendation | Chinese |
| Nagarajan 2025 | BERT hybrid | Academic collaboration rec | English |
| Rosenberger 2025 (CareerBERT) | Fine-tuned BERT | Job/resume matching | English/European |
| Zhang 2025 (Chain-of-Factors) | Transformer multi-factor | Paper-reviewer matching | English |

### What no paper does (= this thesis's gap):
1. **No paper uses BGE-M3, E5-multilingual-large-instruct, or Qwen3-Embedding for supervisor/expert recommendation** — the most capable multilingual embedding models are absent from this domain.
2. **No paper addresses Indonesian academic context** with multilingual dense retrieval that handles code-mixed text (Indonesian/English) natively.
3. **No paper compares multiple next-gen embedding models** (BGE-M3 vs E5 vs Qwen) head-to-head for the academic matching task.
4. **Existing supervisor rec systems** either use TF-IDF/keyword methods or BERT that requires fine-tuning; **zero-shot dense retrieval** with pre-trained multilingual models is untested.

### Thesis novelty statement:
> "Berbeda dengan penelitian sebelumnya yang menggunakan TF-IDF \cite{dasri2025twoway}, SBERT \cite{gundogan2023journal}, atau BERT yang di-fine-tune pada domain tertentu \cite{wang2025avrd}, penelitian ini mengeksplorasi penggunaan model embedding generasi terbaru yang bersifat multilingual zero-shot — yaitu BGE-M3 \cite{chen2024bgem3}, E5-multilingual-large-instruct \cite{wang2024e5}, dan Qwen3-Embedding \cite{qwen3embedding} — untuk sistem rekomendasi pembimbing skripsi pada konteks akademik Indonesia."

---

## Action Items

- [ ] Add `gundogan2023journal` and `rosenberger2025careerbert` to ref.bib (highest priority — verified, well-cited)
- [ ] Optionally add `zhang2025chainoffactors` and `fahrudin2025sentencetransformers` if table has room
- [ ] Update closing paragraph of Penelitian Terkait (bab2.tex ~line 377) to articulate the gap above
- [ ] Consider adding gap statement as a subsection or the last paragraph of BAB 2
