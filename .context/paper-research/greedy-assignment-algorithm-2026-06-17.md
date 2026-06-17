---
name: greedy-assignment-algorithm-2026-06-17
description: Verified papers on greedy algorithms for capacity-constrained assignment — for LT1 fix in bab2.tex (Algoritma Greedy subsection)
metadata:
  type: reference
---

# Found Papers: Greedy Algorithm for Capacity-Constrained Assignment

**Total found:** 5 papers | **Date:** 2026-06-17 | **Filter:** 2020+ | **Purpose:** LT1 — bab2.tex `\subsection{Algoritma Greedy}`

---

## Paper 1 — Maashi 2020 (PRIORITY: HIGH)

**Title:** Solving Student-Project Research Assignment Problems Using A Novel Greedy Linear Heuristic Algorithm: A Case Study at King Saud University, Riyadh Saudi Arabia

**Author:** Mashael S. Maashi (sole author)

**Year:** 2020

**DOI:** `10.21786/bbrc/13.3/27` ✅ verified via OpenAlex + CrossRef

**Journal:** Bioscience Biotechnology Research Communications, Vol. 13, Issue 3, pp. 1168–1173

**Citations:** 3

**Open Access:** No (closed access)

**Abstract:** Not retrievable via API.

**Relevance to thesis:** HIGHEST — directly proposes and evaluates a greedy linear heuristic algorithm for student-project research assignment at university scale with capacity and preference constraints. Maps directly onto the thesis system's `_solve_assignment` greedy solver and serves as the primary citation for justifying greedy as a valid approach for this class of problem.

**BibTeX key:** `maashi2020greedy`

---

## Paper 2 — Duvignau, Gillet, Klasing 2023 (PRIORITY: HIGH)

**Title:** Greediness is not always a vice: Efficient Discovery Algorithms for Assignment Problems

**Authors:** Romaric Duvignau, Noël Gillet, Ralf Klasing

**Year:** 2023

**DOI:** `10.1016/j.procs.2023.08.212` ✅ verified via CrossRef + OpenAlex

**Journal:** Procedia Computer Science (Elsevier), Vol. 223, pp. 43–52

**Citations:** 5

**Open Access:** Yes (Diamond OA, CC BY-NC-ND 4.0)

**arXiv PDF:** https://arxiv.org/pdf/2410.09434v1

**Abstract:** Addresses assignment problems in bipartite graphs where edge weights must be discovered through queries. Develops greedy discovery algorithms that minimize queried weights while providing solution guarantees. Motivated by peer-to-peer energy-sharing communities.

**Relevance to thesis:** HIGH — provides theoretical analysis of greedy algorithms applied to bipartite assignment problems (the same graph structure as student-supervisor assignment). Establishes performance bounds for greedy solutions, giving the theoretical underpinning for `\subsection{Algoritma Greedy}` in bab2. Most cited among the verified papers on this topic.

**BibTeX key:** `duvignau2023greediness`

---

## Paper 3 — Ramotsisi, Kgomotso, Seboni 2022 (PRIORITY: MEDIUM-HIGH)

**Title:** An Optimization Model for the Student-to-Project Supervisor Assignment Problem — The Case of an Engineering Department

**Authors:** Johnson Ramotsisi, Mompoloki Kgomotso, Lone Seboni

**Year:** 2022

**DOI:** `10.1155/2022/9415210` ✅ verified via OpenAlex + CrossRef

**Journal:** Journal of Optimization (Hindawi/Wiley)

**Citations:** 5

**Open Access:** Yes (Gold OA)

**Abstract (excerpt):** "Empirical studies on the topic of assigning university project students to supervisors are currently underexplored. Such studies are critical [to the] success of both the student and the university. Whilst extant research on this topic has contributed to an understanding of student assignments, what appears to be missing is application of a comprehensive framework to inform formulation and validation of a robust solution approach that takes account of both supervisor preferences, to optimize a real-life student-to-project assignment problem."

**Relevance to thesis:** MEDIUM-HIGH — directly models student-to-supervisor assignment with supervisor preferences and capacity constraints. Provides an optimization framing that contextualizes the thesis's heuristic (greedy) approach relative to exact methods. Useful for framing why a greedy heuristic is sufficient at the thesis's problem scale (N≈170, M=14).

**BibTeX key:** `ramotsisi2022optimization`

---

## Paper 4 — Şimşek 2021 (PRIORITY: MEDIUM)

**Title:** A decision support tool for the student–supervisor allocation problem of postgraduate programs

**Author:** Ahmet Bahadır Şimşek (single author)

**Year:** 2021

**DOI:** `10.1016/j.eswa.2021.116068` ⚠️ needs manual verification — DOI found via OpenAlex title search only; NOT further verified via direct DOI lookup. The initially suspected DOI `10.1016/j.eswa.2021.115719` was confirmed WRONG (belongs to a different portfolio paper). Use DOI `116068` with caution.

**Journal:** Expert Systems with Applications (Elsevier)

**Citations:** 4

**Open Access:** No (closed access)

**Abstract:** Not reconstructed.

**Relevance to thesis:** MEDIUM — directly addresses student-supervisor allocation as a decision support problem; compares allocation strategies; uses capacity constraints on supervisors. Relevant but requires DOI manual verification before citing.

**BibTeX key:** `simsek2021decision`

---

## Paper 5 — Ayegba, Olaosebikan, Manlove 2025/2026 (PRIORITY: LOWER)

**Title:** Structural aspects of the Student Project Allocation problem

**Authors:** Peace Ayegba, Sofiat Olaosebikan, David F. Manlove

**Year:** 2025 (arXiv submitted January 2025); formally published January 2026

**DOI (journal):** `10.1016/j.dam.2025.10.034` ✅ verified via CrossRef

**DOI (arXiv):** `10.48550/arxiv.2501.18343`

**Journal:** Discrete Applied Mathematics (Elsevier), Vol. 379, pp. 748–767

**Citations:** 0

**Open Access:** Yes (CC BY 4.0)

**Abstract:** Studies the Student Project Allocation problem with lecturer preferences over students (SPA-S). Proves the set of all stable matchings forms a distributive lattice. Introduces meta-rotations to describe relationships between stable matchings.

**Relevance to thesis:** LOWER (theoretical) — provides the formal combinatorial structure (distributive lattice of stable matchings, meta-rotations) of the SPA problem with capacity constraints. Too theoretical for direct citation in the greedy subsection, but useful as background if bab2 discusses problem formalization. Zero citations limits utility for credibility.

**BibTeX key:** `ayegba2025structural`

---

### Not Found / Unverified

| Paper | Reason for Exclusion |
|---|---|
| Joshi & Jadav — "A Greedy Approach for Assignment of Student Groups to Projects" | Year unknown (likely pre-2020), no DOI found, not indexed in OpenAlex/CrossRef/arXiv. Excluded per instructions. |

---

### Recommended Citations for bab2 LT1

For the `\subsection{Algoritma Greedy}` subsection in bab2.tex, cite **Maashi (2020)** as the primary reference — it is the most directly relevant paper, proposing a greedy linear heuristic for the exact same problem class (student-project assignment at university scale with capacity constraints), making it the strongest justification for the thesis's algorithmic choice. Pair it with **Duvignau et al. (2023)** for theoretical grounding, as that paper formally analyzes greedy performance bounds on bipartite assignment problems (the mathematical structure underlying student-supervisor allocation). Optionally add **Ramotsisi et al. (2022)** to contextualize why a heuristic is appropriate when exact optimization is feasible but unnecessary at the small scale of N≈170 students and M=14 supervisors.

**Do not cite** Ayegba et al. (2025/2026) in the greedy subsection — it is too theoretical and has zero citations. Verify Şimşek (2021) DOI manually before including it.

---

### BibTeX Entries

```bibtex
@article{maashi2020greedy,
  author    = {Mashael S. Maashi},
  title     = {Solving Student-Project Research Assignment Problems Using A Novel Greedy Linear Heuristic Algorithm: A Case Study at King Saud University, Riyadh Saudi Arabia},
  journal   = {Bioscience Biotechnology Research Communications},
  volume    = {13},
  number    = {3},
  pages     = {1168--1173},
  year      = {2020},
  doi       = {10.21786/bbrc/13.3/27}
}

@article{duvignau2023greediness,
  author    = {Romaric Duvignau and No{\"e}l Gillet and Ralf Klasing},
  title     = {Greediness is not always a vice: Efficient Discovery Algorithms for Assignment Problems},
  journal   = {Procedia Computer Science},
  volume    = {223},
  pages     = {43--52},
  year      = {2023},
  doi       = {10.1016/j.procs.2023.08.212},
  note      = {Diamond OA, CC BY-NC-ND 4.0. arXiv preprint: \url{https://arxiv.org/pdf/2410.09434v1}}
}

@article{ramotsisi2022optimization,
  author    = {Johnson Ramotsisi and Mompoloki Kgomotso and Lone Seboni},
  title     = {An Optimization Model for the Student-to-Project Supervisor Assignment Problem---The Case of an Engineering Department},
  journal   = {Journal of Optimization},
  year      = {2022},
  doi       = {10.1155/2022/9415210},
  note      = {Hindawi/Wiley, Gold OA}
}

@article{simsek2021decision,
  author    = {Ahmet Bahadir {\c{S}}im{\c{s}}ek},
  title     = {A decision support tool for the student--supervisor allocation problem of postgraduate programs},
  journal   = {Expert Systems with Applications},
  year      = {2021},
  doi       = {10.1016/j.eswa.2021.116068},
  note      = {WARNING: DOI found via title search only -- manual verification required before citing}
}

@article{ayegba2025structural,
  author    = {Peace Ayegba and Sofiat Olaosebikan and David F. Manlove},
  title     = {Structural aspects of the Student Project Allocation problem},
  journal   = {Discrete Applied Mathematics},
  volume    = {379},
  pages     = {748--767},
  year      = {2026},
  doi       = {10.1016/j.dam.2025.10.034},
  note      = {arXiv: \url{https://arxiv.org/abs/2501.18343}. CC BY 4.0.}
}
```
