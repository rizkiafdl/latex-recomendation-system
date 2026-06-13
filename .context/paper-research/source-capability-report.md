# Academic Source Capability Report

**Date:** 2026-06-12  
**Test query:** "supervisor recommendation semantic similarity embedding"  
**Purpose:** Map which sources are usable for automated paper discovery in thesis research

---

## Summary Table

| Source | Status | Abstracts | PDF | Rate Limit | Best For |
|---|---|---|---|---|---|
| arXiv API | ✅ Works | ✅ Full | ⚠️ Binary only | None observed | CS/ML preprints |
| OpenAlex API | ✅ Works | ⚠️ Partial | ❌ | None observed | DOI lookup, citation count |
| CrossRef API | ✅ Works | ⚠️ Inconsistent | ❌ | None observed | DOI verification, metadata |
| PubMed (NCBI) | ✅ Works | ✅ Full via efetch | ❌ | None observed | Biomedical domain |
| WebSearch | ✅ Works | ❌ Snippets only | ❌ | None observed | Discovery by keyword |
| DBLP API | ✅ Works | ❌ | ❌ | None observed | CS conference/journal papers |
| Semantic Scholar | ❌ Rate limited | ✅ (when working) | ❌ | 429 immediately | All fields (needs API key) |
| CORE | ❌ Auth required | — | — | 403 Forbidden | Open-access full text |
| Unpaywall | ❌ Error (422) | — | — | — | Free PDF links for DOIs |
| ResearchGate | ❌ Blocked | — | — | 403 Forbidden | — |
| IEEE Xplore | ❌ Bot blocked | — | — | 418 Teapot | — |
| ACM DL | ❌ Blocked | — | — | 403 Forbidden | — |

---

## Per-Source Detail

---

### 1. arXiv API ✅
**Endpoint:** `https://export.arxiv.org/api/query?search_query=...&max_results=5`  
**Auth required:** No  
**What comes back:** Title, all authors, submission date, arXiv ID, full abstract, PDF link  
**PDF access:** URL is accessible but content is compressed binary — text is NOT extractable via WebFetch  
**Query quality:** Results are loosely ranked by relevance; broad queries return tangentially related papers  
**Verdict:** Best free source for NLP/ML preprints. Abstract is fully readable. Use for papers not yet published in journals.

**Sample result (relevant):**
```
Title: Semantic Similarity from Natural Language and Ontology Analysis
Authors: Harispe, Ranwez, Janaqi, Montmain
Year: 2017 | arXiv: 1704.05295
Abstract: Central to this quest is to give machines the ability to estimate the
likeness or similarity between things in the way human beings estimate the
similarity between stimuli...
PDF: https://arxiv.org/pdf/1704.05295v1
```

---

### 2. OpenAlex API ✅
**Endpoint:** `https://api.openalex.org/works?search=...&per-page=5&select=...`  
**Auth required:** No  
**What comes back:** Title, year, DOI, citation count, authors  
**Abstracts:** Stored as `abstract_inverted_index` (inverted word-position map) — partially reconstructable but needs post-processing  
**DOI lookup:** Supports `GET /works/https://doi.org/<doi>` for exact paper lookup → returns full abstract  
**Verdict:** Best for verifying a known DOI, getting citation counts, and cross-referencing authors. Abstract reconstruction from inverted index needs a script.

**Sample result (highly relevant):**
```
Title: Research Supervisor Recommendation System Based on Topic Conformity
Authors: Rismanto, Syulistyo, Agusta
Year: 2020 | DOI: 10.5815/ijmecs.2020.01.04 | Citations: 21
Abstract (via DOI lookup): System employs TF-IDF + cosine similarity to match
students with supervisors. 75% accuracy on 20 test cases.
Open Access: YES — PDF: http://www.mecs-press.org/ijmecs/ijmecs-v12-n1/IJMECS-V12-N1-4.pdf
```

---

### 3. CrossRef API ✅
**Endpoint:** `https://api.crossref.org/works?query=...&rows=5&select=title,abstract,DOI,...`  
**Auth required:** No (polite pool with email param recommended)  
**What comes back:** Title, DOI, publication date, authors, citation count  
**Abstracts:** Inconsistent — many papers have no abstract in CrossRef  
**Verdict:** Best for DOI verification and citation counts. Not reliable for abstracts. Combine with OpenAlex DOI lookup for full metadata.

**Sample results:**
```
- Job Recommendation from Semantic Similarity of LinkedIn Users' Skills (2016)
  DOI: 10.5220/0005702302700277 | Citations: 19
- A Personalized Recommendation Algorithm on Integration of Item Semantic Similarity
  and Item Rating Similarity (2011) | Citations: 16
```

---

### 4. PubMed / NCBI ✅
**Endpoints:**
- `eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi` → returns PMIDs
- `eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?id=PMID1,PMID2` → returns full XML with abstracts

**Auth required:** No  
**What comes back:** Full abstract, all authors, journal, year, PMID  
**Domain:** Biomedical / clinical — limited relevance for CS/recommendation systems  
**Query on "recommendation system embedding matching":** 46 results found  
**Verdict:** Fully functional pipeline (search → fetch abstracts). Only useful for your thesis if you need medical/health informatics papers. Full abstracts are cleanly retrievable.

**Sample result:**
```
PMID: 42147035
Title: An explainable graph retrieval augmented generation framework for
personalized nutrition recommendation
Journal: Frontiers in Artificial Intelligence, 2026
Abstract: Graph-centric decision-support using Neo4j + cosine similarity-based
ranking... [full abstract accessible]
```

---

### 5. WebSearch ✅
**Method:** `WebSearch` tool with targeted query  
**What comes back:** Titles, URLs, short snippets  
**Best use:** Discovery — find paper titles and URLs, then use OpenAlex/CrossRef/arXiv to fetch full metadata  
**Limitation:** Snippets only, no structured data, no abstracts  
**Verdict:** Best first step for discovery. Pair with OpenAlex DOI lookup for full details.

**Found directly relevant papers:**
```
1. "Recommendation System to Propose Final Project Supervisors using
   Cosine Similarity Matrix" — ResearchGate URL found
2. "A Content-Based Thesis Supervisor Recommendation System Based on
   Research Interest Clustering and Cosine Similarity" — ResearchGate URL found
3. "Recommendation systems with LLM-based semantic embeddings and FAISS
   similarity search" — ScienceDirect
```

---

### 6. DBLP API ✅ (but zero hits on this query)
**Endpoint:** `https://dblp.org/search/publ/api?q=...&format=json&h=5`  
**Auth required:** No  
**What comes back:** Title, authors, venue, year, DOI/URL  
**Query result:** 0 hits for "supervisor recommendation semantic similarity"  
**Verdict:** API works and is clean, but DBLP indexes CS papers only and the query needs to match paper titles closely. Better for exact author/title lookups than broad topic searches.

---

### 7. Semantic Scholar ❌ (rate limited)
**Status:** HTTP 429 on every attempt  
**Why:** Free tier without API key is severely rate-limited  
**Fix:** Register at `semanticscholar.org/product/api` for a free API key → 1 request/second  
**Capability (when working):** Title, full abstract, year, authors, citation count, DOI + arXiv ID in one call  
**Verdict:** Would be the best single source if rate limit is resolved. High priority to get API key.

---

### 8. CORE ❌ (API key required)
**Status:** HTTP 403  
**Fix:** Register at `core.ac.uk/services/api` for a free API key  
**Capability (with key):** Full-text open-access PDFs, abstracts, metadata  
**Verdict:** High value if key is obtained — one of the few sources that provides actual PDF text.

---

### 9. Unpaywall ❌ (422 error)
**Status:** HTTP 422 Unprocessable Entity  
**Likely cause:** DOI format issue or test email rejected  
**Fix:** Use format `https://api.unpaywall.org/v2/{DOI}?email=youremail@gmail.com`  
**Capability (when working):** Given any DOI, returns whether a free legal PDF exists and the direct download URL  
**Verdict:** Worth fixing — it turns any DOI into a free PDF link when one exists.

---

### 10–12. ResearchGate, IEEE Xplore, ACM DL ❌
- **ResearchGate:** 403 Forbidden — hard block against automated access
- **IEEE Xplore:** 418 I'm a Teapot — playful anti-bot response, completely blocked
- **ACM DL:** 403 Forbidden — blocked
- **Verdict:** These three cannot be scraped. Manual access only.

---

## Recommended Pipeline for Thesis Paper Discovery

```
Step 1 — DISCOVER
  WebSearch("thesis topic keywords site:arxiv.org OR researchgate.net")
  → collect paper titles and URLs

Step 2 — FETCH METADATA
  OpenAlex: GET /works?search=<title>  →  DOI + citation count + abstract
  CrossRef:  GET /works?query=<title>  →  confirm DOI + authors

Step 3 — GET FULL ABSTRACT (if not in OpenAlex)
  OpenAlex DOI lookup: GET /works/https://doi.org/<doi>
  OR arXiv API if preprint: export.arxiv.org/api/query?search_query=<title>

Step 4 — GET FREE PDF (optional)
  Unpaywall: GET /v2/<doi>?email=<your_email>  →  oa_url if available
  arXiv: direct PDF link from Step 3 result

Step 5 — VERIFY & CITE
  CrossRef DOI confirmation → safe to put in ref.bib
```

---

## Papers Found Directly Relevant to Thesis

| Title | Year | Source | DOI / URL |
|---|---|---|---|
| Research Supervisor Recommendation System Based on Topic Conformity | 2020 | OpenAlex | 10.5815/ijmecs.2020.01.04 |
| Recommendation System to Propose Final Project Supervisors using Cosine Similarity Matrix | 2022 | WebSearch | researchgate.net/publication/365580353 |
| A Content-Based Thesis Supervisor Recommendation System Based on Research Interest Clustering and Cosine Similarity | 2024 | WebSearch | researchgate.net/publication/399928395 |
| Recommendation systems with LLM-based semantic embeddings and FAISS similarity search | 2025 | WebSearch | sciencedirect.com/science/article/abs/pii/S0925231225014250 |
| Semantic Similarity from Natural Language and Ontology Analysis | 2017 | arXiv | arxiv.org/abs/1704.05295 |

---

## To Unlock More (Requires One-Time Registration)

| Service | URL | Time to Get Key | Unlocks |
|---|---|---|---|
| Semantic Scholar API | semanticscholar.org/product/api | ~1 day | Best all-in-one paper search |
| CORE API | core.ac.uk/services/api | Instant | Open-access full PDF text |
| Unpaywall | unpaywall.org/products/api | Instant (just email) | Free PDF links for any DOI |
