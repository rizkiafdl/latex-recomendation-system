---
name: uml-notation-symbols-2026-06-16
description: Verified academic sources for UML diagram symbol tables (Use Case, Activity, Class Diagram, ERD) — needed for SD1 fix in bab2.tex Landasan Teori; all DOIs confirmed via OpenAlex + CrossRef
metadata:
  type: project
---

# Found Papers: UML Diagram Notation Symbols

**Total found:** 6 papers/books verified | **Date:** 2026-06-16 | **Filter:** 2020+
**Context:** Needed for SD1 fix — adding symbol tables to bab2.tex §Use Case Diagram, §Activity Diagram, §Class Diagram, §ERD

---

## Recommended Citation Map (for SD1 fix)

| Diagram | Recommended Citation | BibTeX Key |
|---|---|---|
| All 4 diagrams | Dennis et al. 2020 (textbook, covers all) | `dennis2020systems` |
| Use Case + Activity | Maatuk & Abdelnabi 2021 (ACM) | `maatuk2021generatinguml` |
| Activity + Class | Metzner 2024 (IEEE CSEE&T) | `metzner2024umlteaching` |
| Class Diagram | De Bari et al. 2024 (ACM/IEEE ESEM) | `debari2024evaluatinguml` |
| ERD | Skavantzos & Link 2025 (ACM SIGMOD) | `skavantzos2025ergraphs` |

**Simplest approach:** Cite `dennis2020systems` for all 4 symbol tables — it covers UML use case, activity, class, and ERD notation in one textbook.

---

## 1. Systems Analysis and Design: An Object-Oriented Approach with UML (6th Ed.)

| Field | Value |
|---|---|
| **Authors** | Dennis, Alan R.; Wixom, Barbara Haley; Tegarden, David P. |
| **Year** | 2020 |
| **Publisher** | John Wiley & Sons |
| **Edition** | 6th |
| **ISBN** | 9781119559917 |
| **DOI** | [NOT FOUND — no CrossRef/OpenAlex DOI for 6th edition] |
| **Citations** | 255 (OpenAlex, attributed to 5th ed. 2016; no separate 6th ed. record) |
| **Open Access** | No |

**Abstract:** Covers system development life cycle with UML-based modeling including use case diagrams, activity diagrams, class diagrams, and ERD. Each chapter teaches analysts to create each diagram type with notation and symbol tables.

**Relevance note:** The primary textbook reference for UML notation in systems analysis — covers all four diagram types used in bab3. The fix in SD1 explicitly specifies `\cite{dennis2020systems}`.

```bibtex
@book{dennis2020systems,
  author    = {Dennis, Alan R. and Wixom, Barbara Haley and Tegarden, David P.},
  title     = {Systems Analysis and Design: An Object-Oriented Approach with {UML}},
  edition   = {6},
  publisher = {John Wiley \& Sons},
  address   = {Hoboken, NJ},
  year      = {2020},
  isbn      = {9781119559917}
}
```

---

## 2. Systematic Teaching of UML and Behavioral Diagrams

| Field | Value |
|---|---|
| **Authors** | Metzner, Anja |
| **Year** | 2024 |
| **Journal/Conference** | 2024 36th International Conference on Software Engineering Education and Training (CSEE&T), IEEE |
| **Location** | Würzburg, Germany |
| **Pages** | 1–5 |
| **DOI** | `10.1109/CSEET62301.2024.10663036` ✅ verified |
| **Citations** | 3 (OpenAlex) |
| **Open Access** | ✅ Free PDF — https://arxiv.org/pdf/2410.17849 (CC BY-NC-ND) |
| **arXiv** | https://arxiv.org/abs/2410.17849 |

**Abstract:** When studying software engineering, learning to create UML diagrams is crucial. Much as architects design buildings with plans, designing software architectures with standardized notation is essential for producing high-quality software. UML diagrams standardize the visualization of software architectures and behavior. This paper presents an investigation into the systematic acquisition of skills required for creating UML diagrams, with particular emphasis on well-formed activity diagrams. It provides numerous exercises using recommended question types and unusual exercise formats related to images — including annotation, hotspot identification, and target positioning tasks.

**Relevance note:** Directly defines and teaches UML diagram notation taxonomy (use case, activity diagrams) in an IEEE-published 2024 paper — strong supplementary citation for activity diagram symbol table in bab2.

```bibtex
@inproceedings{metzner2024umlteaching,
  author    = {Metzner, Anja},
  title     = {Systematic Teaching of {UML} and Behavioral Diagrams},
  booktitle = {2024 36th International Conference on Software Engineering
               Education and Training ({CSEE\&T})},
  year      = {2024},
  pages     = {1--5},
  publisher = {IEEE},
  address   = {W{\"u}rzburg, Germany},
  doi       = {10.1109/CSEET62301.2024.10663036},
  url       = {https://arxiv.org/pdf/2410.17849},
  note      = {arXiv:2410.17849}
}
```

---

## 3. Generating UML Use Case and Activity Diagrams Using NLP Techniques and Heuristics Rules

| Field | Value |
|---|---|
| **Authors** | Maatuk, Abdelsalam M.; Abdelnabi, Esra A. |
| **Year** | 2021 |
| **Journal/Conference** | DATA'21: International Conference on Data Science, E-learning and Information Systems (ACM) |
| **Location** | Ma'an, Jordan |
| **Pages** | 271–277 |
| **DOI** | `10.1145/3460620.3460768` ✅ verified |
| **Citations** | 38 (OpenAlex) — 94.5th percentile, FWCI 3.92 |
| **Open Access** | ❌ Paywalled (ACM Digital Library only) |

**Abstract:** The process of generating Unified Modeling Language (UML) Diagrams from Natural Language (NL) requirements is considered a complex and challenging task. Software requirements specification is often written in NL format, which causes potential problems. Requirement's analysts analyze and process natural language requirements manually to extract the UML elements. This paper proposes an approach to facilitate the NL requirements analysis process and UML diagrams extraction from NL textual requirements using natural language processing (NLP) techniques and heuristics rules. This approach focuses on generating use-case and activity diagrams.

**Relevance note:** Enumerates the core UML elements of use case diagrams (actors, use cases, relationships) and activity diagrams (actions, decisions, flows) in the context of NLP extraction — good complementary citation for both diagram type symbol tables.

```bibtex
@inproceedings{maatuk2021generatinguml,
  author    = {Maatuk, Abdelsalam M. and Abdelnabi, Esra A.},
  title     = {Generating {UML} Use Case and Activity Diagrams Using {NLP} Techniques and Heuristics Rules},
  booktitle = {Proceedings of the International Conference on Data Science, E-learning and Information Systems 2021},
  series    = {DATA'21},
  year      = {2021},
  pages     = {271--277},
  publisher = {ACM},
  address   = {New York, NY, USA},
  doi       = {10.1145/3460620.3460768},
  url       = {https://doi.org/10.1145/3460620.3460768}
}
```

---

## 4. Evaluating Large Language Models in Exercises of UML Class Diagram Modeling

| Field | Value |
|---|---|
| **Authors** | De Bari, Daniele; Garaccione, Giacomo; Coppola, Riccardo; Torchiano, Marco; Ardito, Luca |
| **Year** | 2024 |
| **Journal/Conference** | ESEM '24: 18th ACM/IEEE International Symposium on Empirical Software Engineering and Measurement |
| **Location** | Barcelona, Spain |
| **Pages** | 393–399 |
| **DOI** | `10.1145/3674805.3690741` ✅ verified |
| **Citations** | 23 (OpenAlex) |
| **Open Access** | ✅ Gold OA — CC-BY — https://doi.org/10.1145/3674805.3690741 |

**Abstract:** Large Language Models (LLM) have rapidly affirmed in the latest years as a means to support or substitute human actors in a variety of tasks. LLM agents can generate valid software models, because of their inherent ability in evaluating textual requirements provided to them in the form of prompts.

**Relevance note:** Uses and validates UML class diagram notation elements (classes, stereotypes, associations, dependencies, multiplicities) — supports class diagram symbol table in bab2.

```bibtex
@inproceedings{debari2024evaluatinguml,
  author    = {De Bari, Daniele and Garaccione, Giacomo and Coppola, Riccardo and Torchiano, Marco and Ardito, Luca},
  title     = {Evaluating Large Language Models in Exercises of {UML} Class Diagram Modeling},
  booktitle = {Proceedings of the 18th ACM/IEEE International Symposium on Empirical Software Engineering and Measurement},
  series    = {ESEM '24},
  year      = {2024},
  pages     = {393--399},
  publisher = {ACM},
  address   = {New York, NY, USA},
  doi       = {10.1145/3674805.3690741},
  url       = {https://doi.org/10.1145/3674805.3690741}
}
```

---

## 5. Connection between UML Use Case Diagrams and UML Class Diagrams: A Matrix Proposal

| Field | Value |
|---|---|
| **Authors** | Alturas, Bráulio |
| **Year** | 2023 |
| **Journal/Conference** | International Journal of Computer Applications in Technology (IJCAT) |
| **Publisher** | Inderscience |
| **Volume / Issue / Pages** | Vol. 72, No. 3, pp. 161–168 |
| **DOI** | `10.1504/IJCAT.2023.133294` ✅ verified |
| **Citations** | 3 (OpenAlex) |
| **Open Access** | ✅ Green OA — http://hdl.handle.net/10071/29345 |

**Abstract:** In recent years, the UML language has been one of the most used to conduct information system analysis and design. Being an object-oriented technique, UML provides a vast set of diagrams. The most used are the use-case diagram and the class diagram. Often, in the modelling of less complex systems, only these two diagrams are used, where one represents the functionalities of the system (use case diagram) and the other the static structure of the system (class diagram). However, it is often difficult to make the connection between the two diagrams, and mainly, it is difficult to verify when one matches the other. This paper proposes a matrix that links the two diagrams.

**Relevance note:** Defines use case and class diagram notation in a systems analysis context — supports both symbol tables but the matrix proposal is tangential to the thesis. Use as supplementary if needed.

```bibtex
@article{alturas2023umlmatrix,
  author    = {Alturas, Br{\'a}ulio},
  title     = {Connection between {UML} Use Case Diagrams and {UML} Class Diagrams: A Matrix Proposal},
  journal   = {International Journal of Computer Applications in Technology},
  volume    = {72},
  number    = {3},
  pages     = {161--168},
  year      = {2023},
  publisher = {Inderscience Publishers},
  doi       = {10.1504/IJCAT.2023.133294},
  url       = {https://doi.org/10.1504/IJCAT.2023.133294}
}
```

---

## 6. Entity/Relationship Graphs: Principled Design, Modeling, and Data Integrity Management of Graph Databases

| Field | Value |
|---|---|
| **Authors** | Skavantzos, Philipp; Link, Sebastian |
| **Year** | 2025 |
| **Journal/Conference** | Proceedings of the ACM on Management of Data (PACMMOD / SIGMOD) |
| **Volume / Issue / Pages** | Vol. 3, No. 1, pp. 1–26 |
| **DOI** | `10.1145/3709690` ✅ verified |
| **Citations** | 7 (OpenAlex) |
| **Open Access** | ❌ Paywalled (no OA copy found) |

**Abstract:** Chen's Entity/Relationship (E/R) framework serves as a standard for well-designed databases. This work defines E/R graphs as property graphs instantiating E/R diagrams. Since the latter constitute a subclass of PG-Schema, E/R modeling provides methodology for designing graph databases that guarantee data integrity, absence of redundancy and update anomalies. The authors provide the first graph semantics for E/R diagrams, together with referential integrity management via directed edges (E/R links) and E/R keys for entity integrity.

**Relevance note:** Uses and formalizes ERD/E-R diagram notation (entities, relationships, keys, cardinality). Strongest recent ACM paper on ERD formal notation. However, its focus is graph databases — use `dennis2020systems` as primary ERD citation; cite this one only if examiner asks for a recent peer-reviewed paper on ERD notation.

```bibtex
@article{skavantzos2025ergraphs,
  author    = {Skavantzos, Philipp and Link, Sebastian},
  title     = {Entity/{R}elationship Graphs: {P}rincipled Design, Modeling, and Data Integrity Management of Graph Databases},
  journal   = {Proceedings of the {ACM} on Management of Data},
  year      = {2025},
  volume    = {3},
  number    = {1},
  pages     = {1--26},
  doi       = {10.1145/3709690},
  issn      = {2836-6573},
  publisher = {Association for Computing Machinery},
  address   = {New York, NY, USA}
}
```

---

## Not Found / Unverified

- **Automated Use Case Diagram Generator (arXiv:2306.06962)** — about NLP/ML generation, not notation reference; excluded.
- **From Image to UML (arXiv:2404.11376)** — about LLM image parsing; too tangential.
- **How LLMs Aid in UML Modeling (arXiv:2404.17739)** — about LLM tools; not a notation reference.

---

## Combined BibTeX Block (ready to paste into ref.bib)

```bibtex
@book{dennis2020systems,
  author    = {Dennis, Alan R. and Wixom, Barbara Haley and Tegarden, David P.},
  title     = {Systems Analysis and Design: An Object-Oriented Approach with {UML}},
  edition   = {6},
  publisher = {John Wiley \& Sons},
  address   = {Hoboken, NJ},
  year      = {2020},
  isbn      = {9781119559917}
}

@inproceedings{metzner2024umlteaching,
  author    = {Metzner, Anja},
  title     = {Systematic Teaching of {UML} and Behavioral Diagrams},
  booktitle = {2024 36th International Conference on Software Engineering
               Education and Training ({CSEE\&T})},
  year      = {2024},
  pages     = {1--5},
  publisher = {IEEE},
  address   = {W{\"u}rzburg, Germany},
  doi       = {10.1109/CSEET62301.2024.10663036},
  url       = {https://arxiv.org/pdf/2410.17849},
  note      = {arXiv:2410.17849}
}

@inproceedings{maatuk2021generatinguml,
  author    = {Maatuk, Abdelsalam M. and Abdelnabi, Esra A.},
  title     = {Generating {UML} Use Case and Activity Diagrams Using {NLP} Techniques and Heuristics Rules},
  booktitle = {Proceedings of the International Conference on Data Science, E-learning and Information Systems 2021},
  series    = {DATA'21},
  year      = {2021},
  pages     = {271--277},
  publisher = {ACM},
  address   = {New York, NY, USA},
  doi       = {10.1145/3460620.3460768},
  url       = {https://doi.org/10.1145/3460620.3460768}
}

@inproceedings{debari2024evaluatinguml,
  author    = {De Bari, Daniele and Garaccione, Giacomo and Coppola, Riccardo and Torchiano, Marco and Ardito, Luca},
  title     = {Evaluating Large Language Models in Exercises of {UML} Class Diagram Modeling},
  booktitle = {Proceedings of the 18th ACM/IEEE International Symposium on Empirical Software Engineering and Measurement},
  series    = {ESEM '24},
  year      = {2024},
  pages     = {393--399},
  publisher = {ACM},
  address   = {New York, NY, USA},
  doi       = {10.1145/3674805.3690741},
  url       = {https://doi.org/10.1145/3674805.3690741}
}

@article{alturas2023umlmatrix,
  author    = {Alturas, Br{\'a}ulio},
  title     = {Connection between {UML} Use Case Diagrams and {UML} Class Diagrams: A Matrix Proposal},
  journal   = {International Journal of Computer Applications in Technology},
  volume    = {72},
  number    = {3},
  pages     = {161--168},
  year      = {2023},
  publisher = {Inderscience Publishers},
  doi       = {10.1504/IJCAT.2023.133294},
  url       = {https://doi.org/10.1504/IJCAT.2023.133294}
}

@article{skavantzos2025ergraphs,
  author    = {Skavantzos, Philipp and Link, Sebastian},
  title     = {Entity/{R}elationship Graphs: {P}rincipled Design, Modeling, and Data Integrity Management of Graph Databases},
  journal   = {Proceedings of the {ACM} on Management of Data},
  year      = {2025},
  volume    = {3},
  number    = {1},
  pages     = {1--26},
  doi       = {10.1145/3709690},
  issn      = {2836-6573},
  publisher = {Association for Computing Machinery},
  address   = {New York, NY, USA}
}
```
