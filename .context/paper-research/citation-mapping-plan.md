# Citation Mapping Plan
> Last updated: 2026-06-12. Pre-verification snapshot — no internet lookup done yet.
> Scope: only citations actually used in thesis text.

---

## Problem Summary

- `ref.bib` has zero usable entries (all placeholder)
- Zero `\cite{}` commands in any .tex file
- 3 entries in old Word bibliography have wrong content
- 3 longtable rows in bab2.tex are hallucinated papers

---

## Cite Map — bab1.tex + bab2.tex

All `\cite{}` insertions needed. bab3–bab5 have no inline citations.

| File | Line | Inline Text | Key | Source Status |
|---|---|---|---|---|
| bab1 | 24 | Fan et al. (2021) | `fan2021automated` | ✅ Word bib OK |
| bab1 | 24 | Rismanto et al. (2020) | `rismanto2020supervisor` | ✅ Word bib OK |
| bab2 | 11 | Ricci, Rokach, dan Shapira (2015) | `ricci2015recommender` | ❌ Missing |
| bab2 | 17 | Russell dan Norvig (2021) | `russell2021ai` | ✅ Word bib OK |
| bab2 | 23 | Mitchell (1997) | `mitchell1997ml` | ❌ Missing |
| bab2 | 29 | LeCun, Bengio, dan Hinton (2015) | `lecun2015deep` | ❌ Missing |
| bab2 | 35 | Jurafsky dan Martin (2023) | `jurafsky2023slp` | ✅ Word bib OK |
| bab2 | 57 | Manning, Raghavan, dan Schütze (2008) | `manning2008ir` | ✅ Word bib OK |
| bab2 | 65 | Salton dan Buckley (1988) | `salton1988tfidf` | ❌ Missing |
| bab2 | 95 | Mikolov et al. (2013) | `mikolov2013word2vec` | ❌ Missing |
| bab2 | 95 | Pennington et al. (2014) | `pennington2014glove` | ❌ Missing |
| bab2 | 95 | Bojanowski et al. (2017) | `bojanowski2017fasttext` | ❌ Missing |
| bab2 | 103 | Vaswani et al. (2017) | `vaswani2017attention` | ✅ Word bib OK |
| bab2 | 105 | Devlin et al. (2019) | `devlin2019bert` | ✅ Word bib OK |
| bab2 | 113 | Devlin et al. (2019) _(2nd use)_ | `devlin2019bert` | ✅ same key |
| bab2 | 121 | Mihalcea et al. (2006) | `mihalcea2006semantic` | ❌ Missing |
| bab2 | 127 | Han, Kamber, dan Pei (2012) | `han2012datamining` | ❌ Missing |
| bab2 | 145 | Reimers dan Gurevych (2019) | `reimers2019sbert` | ❌ Missing |
| bab2 | 151 | Chen et al. (2024) | `chen2024bgem3` | ⚠️ Word bib wrong (BAAI org) |
| bab2 | 163 | Wang et al. (2024) | `wang2024e5` | ⚠️ Word bib wrong (port forwarding paper) |
| bab2 | 169 | Shani dan Gunawardana (2011) | `shani2011evaluating` | ❌ Missing |
| bab2 | 195 | Herlocker et al. (2004) | `herlocker2004recall` | ❌ Missing |
| bab2 | 215 | Voorhees (1999) | `voorhees1999mrr` | ❌ Missing |
| bab2 | 237 | Manning et al. (2008) _(2nd use)_ | `manning2008ir` | ✅ same key |
| bab2 | 253 | Van Rossum dan Drake (2009) | `vanrossum2009python` | ❌ Missing |
| bab2 | 261 | Flanagan (2020) | `flanagan2020javascript` | ❌ Missing |
| bab2 | 269 | Grinberg (2018) | `grinberg2018flask` | ❌ Missing |
| bab2 | 277 | Paszke et al. (2019) | `paszke2019pytorch` | ❌ Missing |
| bab2 | 285 | Owens (2006) | `owens2006sqlite` | ❌ Missing |
| bab2 | 293 | Copeland (2008) | `copeland2008sqlalchemy` | ❌ Missing |
| bab2 | 301 | Reimers dan Gurevych (2019) _(2nd use)_ | `reimers2019sbert` | ✅ same key |
| bab2 | 309 | Kurose dan Ross (2017) | `kurose2017networking` | ❌ Missing |
| bab2 | 315 | Mell dan Grance (2011) | `mell2011nist` | ❌ Missing |
| bab2 | 323 | Dennis et al. (2020) | `dennis2020systems` | ✅ Word bib OK |
| bab2 | 327 | Dennis et al., 2020 _(2nd use)_ | `dennis2020systems` | ✅ same key |
| bab2 | 331 | Dennis et al., 2020 _(3rd use)_ | `dennis2020systems` | ✅ same key |
| bab2 | 335 | Dennis et al., 2020 _(4th use)_ | `dennis2020systems` | ✅ same key |
| bab2 | 362 | Tang et al., 2016 | `tang2016supervisor` | ❌ Missing — need verify |
| bab2 | 364 | Huang et al., 2019 | `huang2019supervisor` | ❌ Missing — need verify |
| bab2 | 366 | Aljohani & Davis, 2020 | `aljohani2020matching` | ❌ Missing — need verify |

### Longtable rows to DELETE (hallucinated — no BibTeX needed)

| Line | Citation | Action |
|---|---|---|
| 368 | Rahman et al., 2023 | Delete row → replace with `hairani2022supervisor` |
| 370 | Li et al., 2022 | Delete row → replace with `sabilillah2024bert` |
| 372 | Zhang et al., 2021 | Delete row → replace with [3rd paper — still needed] |

---

## ref.bib Keys Needed (38 total)

✅ = have source data | 🔍 = need internet lookup | ⚠️ = wrong entry in Word bib, need correct BibTeX

| Key | Paper | Status | Expected Source |
|---|---|---|---|
| `fan2021automated` | Fan et al. (2021) Global J Engineering Education | ✅ | Word bib |
| `rismanto2020supervisor` | Rismanto et al. (2020) IJMECS | ✅ | DOI: 10.5815/ijmecs.2020.01.04 |
| `ricci2015recommender` | Ricci et al. (2015) Recommender Systems Handbook | 🔍 | Springer book |
| `russell2021ai` | Russell & Norvig (2021) AI: Modern Approach 4e | ✅ | Word bib |
| `mitchell1997ml` | Mitchell (1997) Machine Learning | 🔍 | McGraw-Hill book |
| `lecun2015deep` | LeCun, Bengio, Hinton (2015) Deep Learning | 🔍 | Nature 521 |
| `jurafsky2023slp` | Jurafsky & Martin (2023) Speech & Language Processing | ✅ | Word bib |
| `manning2008ir` | Manning, Raghavan, Schütze (2008) Intro to IR | ✅ | Word bib |
| `salton1988tfidf` | Salton & Buckley (1988) TF-IDF | 🔍 | Information Processing & Management |
| `mikolov2013word2vec` | Mikolov et al. (2013) Word2Vec | 🔍 | arXiv:1301.3781 |
| `pennington2014glove` | Pennington et al. (2014) GloVe | 🔍 | EMNLP 2014 |
| `bojanowski2017fasttext` | Bojanowski et al. (2017) FastText | 🔍 | TACL 2017 |
| `vaswani2017attention` | Vaswani et al. (2017) Attention Is All You Need | ✅ | Word bib |
| `devlin2019bert` | Devlin et al. (2019) BERT | ✅ | Word bib |
| `mihalcea2006semantic` | Mihalcea et al. (2006) Semantic Similarity | 🔍 | ACL Anthology |
| `han2012datamining` | Han, Kamber, Pei (2012) Data Mining 3e | 🔍 | Morgan Kaufmann |
| `reimers2019sbert` | Reimers & Gurevych (2019) SBERT | 🔍 | arXiv:1908.10084 |
| `chen2024bgem3` | Chen et al. (2024) BGE-M3 | ⚠️ | arXiv:2309.07597 |
| `wang2024e5` | Wang et al. (2024) multilingual-e5-large-instruct | ⚠️ | arXiv:2402.05672 |
| `qwen2024embedding` | Qwen Team (2024) Qwen3-Embedding-0.6B | ⚠️ | HuggingFace model card |
| `shani2011evaluating` | Shani & Gunawardana (2011) evaluating rec sys | 🔍 | Chapter in Ricci 2011 handbook |
| `herlocker2004recall` | Herlocker et al. (2004) Recall@K | 🔍 | ACM TOIS |
| `voorhees1999mrr` | Voorhees (1999) MRR/TREC | 🔍 | TREC proceedings |
| `vanrossum2009python` | Van Rossum & Drake (2009) Python | 🔍 | Python Software Foundation |
| `flanagan2020javascript` | Flanagan (2020) JavaScript Definitive Guide | 🔍 | O'Reilly book |
| `grinberg2018flask` | Grinberg (2018) Flask Web Development | 🔍 | O'Reilly book |
| `paszke2019pytorch` | Paszke et al. (2019) PyTorch NeurIPS | 🔍 | NeurIPS 2019 |
| `owens2006sqlite` | Owens (2006) Definitive Guide to SQLite | 🔍 | O'Reilly book |
| `copeland2008sqlalchemy` | Copeland (2008) Essential SQLAlchemy | 🔍 | O'Reilly book |
| `kurose2017networking` | Kurose & Ross (2017) Computer Networking 7e | 🔍 | Pearson book |
| `mell2011nist` | Mell & Grance (2011) NIST SP 800-145 | 🔍 | NIST publication |
| `dennis2020systems` | Dennis, Wixom, Roth (2020) Systems Analysis & Design | ✅ | Word bib |
| `tang2016supervisor` | Tang et al. (2016) supervisor recommendation | 🔍 | Need verify + BibTeX |
| `huang2019supervisor` | Huang et al. (2019) supervisor semantic similarity | 🔍 | Need verify + BibTeX |
| `aljohani2020matching` | Aljohani & Davis (2020) text mining matching | 🔍 | Need verify + BibTeX |
| `hairani2022supervisor` | Hairani & Mujahid (2022) SISTEMASI | ✅ | DOI/URL known |
| `sabilillah2024bert` | Sabilillah et al. (2024) Edumatic | ✅ | URL known |
| `[tbd3rd]` | 3rd longtable replacement (2021–2023) | 🔍 | Need to find |
| `brooke1996sus` | Brooke (1996) SUS — bab5 Saran only | ✅ | Word bib |

**Counts: 12 have source data ✅ | 24 need lookup 🔍 | 3 need correction ⚠️**
