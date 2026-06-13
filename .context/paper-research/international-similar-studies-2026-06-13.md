# International Similar Studies — Supervisor/Advisor Recommendation
> Generated: 2026-06-13 | 4-agent parallel swarm + 4-agent verification
> Goal: Replace/prioritize international papers over Indonesian journals in Penelitian Terkait table (bab2.tex)

---

## Current Table Status

| Row | Paper | Venue | Verdict |
|---|---|---|---|
| 1 | Zhang et al. (2016) `zhang2016supervisor` | HICSS 2016 (IEEE) | ✅ Keep — already international |
| 2 | Dharmawati et al. (2020) | JUTISI | 🇮🇩 Indonesian local |
| 3 | Agustina et al. (2020) | Jurnal Repositor | 🇮🇩 Indonesian local |
| 4 | Hairani & Mujahid (2022) | SISTEMASI | 🇮🇩 Indonesian local |
| 5 | Sabilillah et al. (2024) | Edumatic | 🇮🇩 Indonesian local |
| 6 | Falah & Suryawan (2022) | Khazanah Informatika | 🇮🇩 Indonesian local |

---

## Verified International Candidates (All DOIs Confirmed)

---

### 1. SPECTER: Document-level Representation Learning using Citation-informed Transformers
| Field | Value |
|---|---|
| **Authors** | Cohan, A.; Feldman, S.; Beltagy, I.; Downey, D.; Weld, D. S. |
| **Year** | 2020 |
| **Venue** | ACL 2020 — Proceedings of the 58th Annual Meeting of the ACL, pp. 2270–2282 |
| **DOI** | `10.18653/v1/2020.acl-main.207` ✅ verified |
| **arXiv** | arXiv:2004.07180 |
| **Citations** | 286+ (CrossRef) |
| **Open Access** | ✅ ACL Anthology |

**Abstract:** Pretrained Transformer (BERT-based) on citation graph signals to generate document-level scientific paper embeddings. Widely adopted by OpenReview for paper-reviewer affinity scoring — structurally identical to supervisor-student matching.

**Relevance:** Foundational method for matching researchers by paper embeddings. Directly analogous to matching student proposals to supervisor publications.

**BibTeX:**
```bibtex
@inproceedings{cohan2020specter,
  author    = {Cohan, Arman and Feldman, Sergey and Beltagy, Iz and Downey, Doug and Weld, Daniel S.},
  title     = {{SPECTER}: Document-level Representation Learning using Citation-informed Transformers},
  booktitle = {Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics},
  pages     = {2270--2282},
  year      = {2020},
  publisher = {Association for Computational Linguistics},
  doi       = {10.18653/v1/2020.acl-main.207},
  url       = {https://aclanthology.org/2020.acl-main.207}
}
```

---

### 2. Empowering College Students to Select Ideal Advisors: A Text-Based Recommendation Model
| Field | Value |
|---|---|
| **Authors** | Wang, X.; Zhou, J.; Jian, L.; Yin, Y.; Li, L. |
| **Year** | 2025 |
| **Venue** | Frontiers in Education, Vol. 10 (Scopus, WoS indexed) |
| **DOI** | `10.3389/feduc.2025.1673956` ✅ verified |
| **Citations** | 0 (published Oct 2025, brand new) |
| **Open Access** | ✅ fully open |

**Abstract (reconstructed):** Proposes AVRD (AdVisor RecommenDation) model using Chinese BERT + unsupervised SimCSE to train advisor corpus embeddings. Applies time-decay weighting to advisor record vectors. Cosine similarity ranks advisors against student interest profiles. Tested on 170 students. Outperforms TF-IDF, LSA, Word2Vec, Qwen, and DeepSeek.

**Relevance:** Most directly on-topic — exact same problem as this thesis (student → advisor recommendation using text embedding + cosine similarity).

**BibTeX:**
```bibtex
@article{wang2025avrd,
  author    = {Wang, Xinmin and Zhou, Jiaxin and Jian, Ling and Yin, Yue and Li, Li},
  title     = {Empowering College Students to Select Ideal Advisors: A Text-Based Recommendation Model},
  journal   = {Frontiers in Education},
  volume    = {10},
  year      = {2025},
  doi       = {10.3389/feduc.2025.1673956},
  url       = {https://doi.org/10.3389/feduc.2025.1673956}
}
```

---

### 3. A BERT Based Hybrid Recommendation System For Academic Collaboration
| Field | Value |
|---|---|
| **Authors** | Nagarajan, S.; Thangaraj, H.; Vashisht, V.; Joshi, E.; Verma, K.; Katariya, D. |
| **Year** | 2025 (conference: ICISS 2024, Dec 20-22, 2024) |
| **Venue** | Springer LNNS — Intelligent Systems and Security (ICISS 2024), pp. 111–125 |
| **DOI** | `10.1007/978-981-96-4273-1_9` ✅ verified |
| **arXiv** | arXiv:2502.15223 |
| **Open Access** | ✅ via arXiv preprint |

**Abstract:** Proposes institution-specific academic profile recommendation comparing TF-IDF, BERT, and hybrid approach. Uses Affinity Propagation clustering with Silhouette score evaluation for academic collaboration matching within a university.

**Relevance:** BERT-based academic profile matching in a university setting — closely related to supervisor-student matching.

**BibTeX:**
```bibtex
@inproceedings{nagarajan2025bert,
  author    = {Nagarajan, Sangeetha and Thangaraj, Harish and Vashisht, Varun and Joshi, Eshaan and Verma, Kanishka and Katariya, Diya},
  title     = {A {BERT} Based Hybrid Recommendation System For Academic Collaboration},
  booktitle = {Proceedings of the International Conference on Intelligent Systems and Security (ICISS 2024)},
  series    = {Lecture Notes in Networks and Systems},
  pages     = {111--125},
  year      = {2025},
  publisher = {Springer Nature Singapore},
  doi       = {10.1007/978-981-96-4273-1_9},
  eprint    = {2502.15223},
  archivePrefix = {arXiv}
}
```

---

### 4. Two-Way Thesis Supervisor Recommendation System Using MapReduce K-Skyband View Queries
| Field | Value |
|---|---|
| **Authors** | Dasri, D.; Annisa, A.; Haryanto, T. |
| **Year** | 2025 |
| **Venue** | JOIV: International Journal on Informatics Visualization, 9(1) (Scopus-indexed) |
| **DOI** | `10.62527/joiv.9.1.2800` ✅ verified |
| **Note** | Journal origin is Indonesian, but Scopus-indexed and internationally co-authored |

**Abstract:** Developed a two-way supervisor recommendation system (students + supervisors both have preferences) using MapReduce K-Skyband view queries. Considers timely graduation challenges. MapReduce 8× faster than Block Nested Loop on large datasets.

**Relevance:** Directly about thesis supervisor recommendation — adds the angle of bidirectional matching and scalability.

**BibTeX:**
```bibtex
@article{dasri2025twoway,
  author    = {Dasri, Dasri and Annisa, Annisa and Haryanto, Toto},
  title     = {Two-Way Thesis Supervisor Recommendation System Using {MapReduce} {K-Skyband} View Queries},
  journal   = {JOIV: International Journal on Informatics Visualization},
  volume    = {9},
  number    = {1},
  pages     = {163--175},
  year      = {2025},
  doi       = {10.62527/joiv.9.1.2800},
  url       = {https://joiv.org/index.php/joiv/article/view/2800}
}
```

---

### 5. Revolutionizing Higher Education: Advanced Recommendation Systems for Project Selection and Supervision
| Field | Value |
|---|---|
| **Authors** | Carvalho, P.; Fernandes, F. E. |
| **Year** | 2026 ⚠️ (not 2024 — APIs confirm 2026) |
| **Venue** | EAI/Springer Innovations in Communication and Computing (book chapter, pp. 3–33) |
| **DOI** | `10.1007/978-3-032-11013-8_1` ✅ verified |

**Relevance:** Web application combining matrix factorization + content-based filtering (cosine similarity + NLP/TF-IDF) for matching students to supervisors and project topics.

**BibTeX:**
```bibtex
@incollection{carvalho2026revolutionizing,
  author    = {Carvalho, Piedade and Fernandes, Francisco Erivaldo},
  title     = {Revolutionizing Higher Education: An In-Depth Exploration of Advanced Recommendation Systems for Project Selection and Supervision},
  booktitle = {Innovations in Communication and Computing},
  pages     = {3--33},
  year      = {2026},
  publisher = {Springer},
  doi       = {10.1007/978-3-032-11013-8_1}
}
```

---

## Recommended Table Composition

Replace 5 of 6 Indonesian rows with international papers, keep zhang2016supervisor:

| Row | Replacement | Venue | Method | Why |
|---|---|---|---|---|
| 1 | `zhang2016supervisor` (keep) | IEEE HICSS 2016 | Personality matching | Already international |
| 2 | `cohan2020specter` (NEW) | ACL 2020 | BERT doc embedding for expert matching | Top ACL venue, 286+ citations, foundational |
| 3 | `nagarajan2025bert` (NEW) | Springer ICISS 2024 | BERT hybrid academic rec | Springer, directly on academic collaboration |
| 4 | `wang2025avrd` (NEW) | Frontiers in Education 2025 | BERT + SimCSE advisor rec | Most on-topic, exact same problem |
| 5 | `dasri2025twoway` (NEW) | JOIV 2025 (Scopus) | MapReduce K-Skyband supervisor rec | Directly about thesis supervisor |
| 6 | Keep 1 Indonesian: `sabilillah2024bert` | Edumatic 2024 | BERT + cosine similarity | Uses BERT — closest method to this thesis |

**Rationale for keeping sabilillah2024bert:** It uses BERT specifically for supervisor recommendation, same domain. Including at least one local study is also common practice in Indonesian theses to show awareness of domestic prior work.

---

## Papers That Were Considered but Not Recommended

| Paper | Reason excluded |
|---|---|
| Carvalho & Fernandes (2026) | Year 2026 — too recent, 0 citations, book chapter only |
| BERTERS (Chaos Solitons & Fractals 2021) | Expert recommendation in general, not student-supervisor specifically |
| AdvisingWise (arXiv 2024) | LLM multi-agent advising assistant — different problem scope |

---

## Action Items

- [ ] Add `cohan2020specter`, `nagarajan2025bert`, `wang2025avrd`, `dasri2025twoway` to `ref.bib`
- [ ] Rewrite longtable rows 2–5 in bab2.tex with these new papers
- [ ] Keep `zhang2016supervisor` (row 1) and `sabilillah2024bert` (row 6)
- [ ] Remove `dharmawati2020aplikasi`, `agustina2020sistem`, `hairani2022supervisor`, `falah2022recommendation` from table (can keep in ref.bib in case reviewer asks)