# System Deep Dive — Step-by-Step Internals

**Date:** 2026-07-29

A detailed walkthrough of every stage in the recommendation pipeline: what data looks like, how calculations work, and the exact logic behind each algorithm.

---

## 1. Full Pipeline Order

Entry point: `services.py::generate_and_store_recommendations()`

```
[1] Load students from DB
[2] Load + enrich supervisor profiles from DB + history
[3] Build text documents (student_document, profile_document)
[4] Encode documents → embedding vectors
[5] Compute cosine similarity matrix (matmul)
[6] Apply SIMILARITY_WEIGHT → weighted_similarity
[7] (Optional) Apply company group bonus → score_matrix
[8] Build capacity plan (min/max caps per supervisor)
[9] Greedy solver → assignment array
[10] Build RecommendationItem list
[11] Evaluate metrics (MRR, Hit@K, nDCG, etc.)
[12] Build rankings JSON (top-N per student)
[13] Write RecommendationRun + Recommendation rows to DB
[14] session.commit()
```

---

## 2. Data Loading

### Students (`services.py::_students_for_recommender`)

Each student becomes a plain dict with these keys:
```python
{
  "student_id": "2501234567",
  "binusian_id": "BN001",
  "name": "Budi Santoso",
  "email": "budi@binus.ac.id",
  "phone": "08123456789",
  "track": "Internship",
  "gpa": 3.75,
  "total_sks": 120,
  "partner_lecturer": "PT Tokopedia",          # ← used in embedding
  "position_topic": "Backend Engineer NodeJS", # ← used in embedding
  "duration": "6 months",
  "job_start_date": "2026-02-01",
  "job_end_date": "2026-07-31",
  "work_schema": "Hybrid",                     # ← used in embedding
  "enrollment_status": "Active",
  "current_supervisor_code": "D6826",          # ← ground truth for evaluation
  "current_supervisor_name": "Karen Etania Saputra",
}
```

### Supervisors (`services.py::_supervisor_profiles_from_db`)

Each supervisor becomes a `SupervisorProfile`:
```python
SupervisorProfile(
  code="D6826",
  name="Karen Etania Saputra, S.Kom., M.Kom.",
  keywords=("general", "free topic", "software engineering", ...),
  labels=("software_engineering", "web_fullstack", "internship", "general_flexible"),
)
```

**Adaptive enrichment** — before running the pipeline, the system reads historical student data already assigned to each supervisor and extracts:
- Top 16 most frequent content tokens (≥3 chars, not stopwords, freq ≥ 2) from past students' documents
- Top 8 most frequent labels (freq ≥ 2) detected from past students' documents

These are appended to the supervisor's keywords/labels at runtime, making the profile adapt to actual supervision patterns over time.

---

## 3. Document Building (`app/rules.py`)

### `normalize_text(value)` — applied to ALL documents

```python
text = str(value).strip().lower()
text = re.sub(r"[^a-z0-9]+", " ", text)   # remove punctuation, keep letters + digits
text = re.sub(r"\s+", " ", text).strip()   # collapse whitespace
```

Example:
```
"PT. Tokopedia — Backend/NodeJS" → "pt tokopedia backend nodejs"
```

### `student_document(student)` — 4 fields joined

```python
parts = [
  student.get("track") or "",           # "Internship"
  student.get("partner_lecturer") or "", # "PT Tokopedia"
  student.get("position_topic") or "",   # "Backend Engineer NodeJS"
  student.get("work_schema") or "",      # "Hybrid"
]
return normalize_text(" ".join(parts))
```

Result: `"internship pt tokopedia backend engineer nodejs hybrid"`

**Fields NOT used:** student_id, name, email, phone, GPA, total_sks, dates.

### `profile_document(profile)` — keywords + labels only

```python
parts = [
  *profile.keywords,  # ("general", "free topic", "software engineering", ...)
  *profile.labels,    # ("software_engineering", "web_fullstack", "internship", ...)
]
return normalize_text(" ".join(parts))
```

Result: `"general free topic software engineering flexible web development application development internship support software engineering web fullstack internship general flexible"`

**Fields NOT used:** supervisor name, code.

### Extra docs (optional, `enable_extra_docs=True`)

`services.py::load_supervisor_extra_docs()` reads `map_2026.xlsx`, groups rows by supervisor code, and concatenates POSITION/TOPIC + PARTNER/LECTURER + TRACK text from actual student placements. This is appended to the supervisor document at runtime for richer representation.

---

## 4. Embedding Encoding (`app/embedding.py`)

### What `encode_batch(texts, task)` returns

```python
encode_kwargs = dict(
  convert_to_numpy=True,
  normalize_embeddings=True,  # ← L2-normalizes every vector to length = 1
  show_progress_bar=False,
)
return self._model.encode(texts, **encode_kwargs)
```

Input:  list of N strings  
Output: numpy array of shape `(N, D)` where D = model's embedding dimension

For `intfloat/multilingual-e5-large-instruct`: D = 1024  
For `BAAI/bge-m3`: D = 1024  
For `Qwen/Qwen3-Embedding-0.6B`: D = 1024

**Concrete shape example** (100 students, 14 supervisors):
```
student_vectors:    shape (100, 1024)  — 100 rows, each 1024 floats
supervisor_vectors: shape  (14, 1024)  — 14 rows, each 1024 floats
```

Each row is a unit vector: `||row|| = 1.0` (guaranteed by `normalize_embeddings=True`).

### What the vector actually looks like

The model maps text meaning into a high-dimensional space where similar meanings point in similar directions. You cannot directly interpret individual dimensions, but conceptually:

```
"internship pt tokopedia backend nodejs" →
  [0.023, -0.118, 0.342, 0.071, ..., -0.055]   (1024 numbers, sums of squares = 1)

"internship binus bandung software engineering" →
  [0.019, -0.103, 0.318, 0.065, ..., -0.047]   (similar direction → high dot product)

"banking finance data analytics" →
  [-0.042, 0.201, -0.031, 0.184, ..., 0.093]   (different direction → low dot product)
```

---

## 5. Cosine Similarity Calculation

### The formula

Standard cosine similarity:
```
cos(A, B) = (A · B) / (||A|| × ||B||)
```

Because `normalize_embeddings=True` forces `||A|| = 1` and `||B|| = 1`:
```
cos(A, B) = A · B       (division by 1 disappears)
```

### Dot product calculation (small 4-dim example)

```
student 0 vector:       [0.6,  0.1,  0.7,  0.2]   (||v|| = 1)
supervisor D6670 vector: [0.7,  0.2,  0.6,  0.1]   (||v|| = 1)
supervisor D6836 vector: [0.1,  0.8,  0.3,  0.5]   (||v|| = 1)

student 0 vs D6670:
  dot = (0.6×0.7) + (0.1×0.2) + (0.7×0.6) + (0.2×0.1)
      =  0.42    +  0.02    +  0.42    +  0.02
      =  0.88   ← high similarity (both "internship software")

student 0 vs D6836:
  dot = (0.6×0.1) + (0.1×0.8) + (0.7×0.3) + (0.2×0.5)
      =  0.06    +  0.08    +  0.21    +  0.10
      =  0.45   ← lower similarity (D6836 is "banking finance")
```

### The matmul does ALL pairs at once

```python
similarity = np.matmul(student_vectors, supervisor_vectors.T)
```

`(N, 1024) × (1024, 14)` → `(N, 14)`

```
similarity matrix (3 students × 3 supervisors example):

              D6670   D6836   D6826
student 0:  [ 0.88,   0.45,   0.71 ]
student 1:  [ 0.39,   0.82,   0.51 ]   ← student 1 best matches D6836 (banking)
student 2:  [ 0.61,   0.53,   0.79 ]
```

Cell `[i, j]` = cosine similarity between student i and supervisor j.  
Range: -1 to 1 (in practice 0 to 1 for normalized positive-sentiment text).

### Weighted similarity

```python
weighted_similarity = similarity * SIMILARITY_WEIGHT   # default 1.0 → no change
score_matrix = weighted_similarity                     # initial score matrix
```

---

## 6. Company Group Bonus (`recommender.py::_apply_company_group_bonus`)

Applied only when `enable_group_bonus=True`.

### Step 1 — Group students by company

```python
key = normalize_text(student.get("partner_lecturer"))
# Skip if key is empty, "-", "none", "na", "n a"
company_map["pt tokopedia"] = [0, 3, 7]   # student indices from same company
```

### Step 2 — Skip groups with < 2 students

Groups of 1 are not cohorts; skip.

### Step 3 — Skip groups with > 6 distinct topics (too diverse)

```python
topic_diversity = {normalize_text(students[i].get("position_topic")) for i in student_indices}
if len(topic_diversity) > 6:
    continue
```

### Step 4 — Find best supervisor for the group

```python
company_scores = score_matrix[student_indices, :]   # rows for this group
mean_scores = company_scores.mean(axis=0)           # average score per supervisor
ranked = np.argsort(-mean_scores, kind="stable")
best_supervisor_idx = int(ranked[0])
```

### Step 5 — Margin check (≥ 0.08 required)

```python
margin = mean_scores[ranked[0]] - mean_scores[ranked[1]]
if margin < 0.08:
    continue   # best supervisor not clear enough winner
```

### Step 6 — Compute and apply bonus

```python
effective_bonus = 0.2 / max(1.0, log2(len(student_indices) + 1))
# Group of 2: 0.2 / log2(3) ≈ 0.2 / 1.585 ≈ 0.126
# Group of 3: 0.2 / log2(4) ≈ 0.2 / 2.000 ≈ 0.100
# Group of 7: 0.2 / log2(8) ≈ 0.2 / 3.000 ≈ 0.067

for student_idx in student_indices:
    group_bonus[student_idx, best_supervisor_idx] += effective_bonus
```

Score matrix after bonus:
```
score_matrix = weighted_similarity + group_bonus
```

Students from same company get a nudge toward the same supervisor, but only if that supervisor clearly dominates (margin ≥ 0.08) and the company group is cohesive (≤ 6 distinct topics).

---

## 7. Capacity Planning (`recommender.py::_build_capacity_plan`)

### Initialization

```python
min_caps = [target_min] * len(supervisors)   # e.g. [10, 10, 10, ..., 10]
max_caps = [target_max] * len(supervisors)   # e.g. [12, 12, 12, ..., 12]
```

Supervisors are ranked by priority order first, then by array index:
```python
ranked = sorted(range(len(codes)), key=lambda i: (priority.get(codes[i], 999), i))
# Priority codes first, then everyone else
```

### Overflow handling (too many students)

```
student_count = 150,  max_total = 14 × 12 = 168  → no overflow
student_count = 180,  max_total = 14 × 12 = 168  → overflow = 12
```

**With priority codes (D2211, D6184, D6826, D1749):**
```
Distribute overflow 12 across 4 priority supervisors (round-robin):
  D2211: max_cap → 15
  D6184: max_cap → 15
  D6826: max_cap → 15
  D1749: max_cap → 15
  (remaining overflow distributed again if needed)
Non-priority supervisors stay hard-capped at 12.
```

**Without priority codes:** overflow distributed round-robin across ALL supervisors.

### Underflow handling (too few students)

```
student_count = 100,  min_total = 14 × 10 = 140  → deficit = 40
```

Reduce min_caps starting from the lowest-priority supervisors (reversed ranked order):
```
Supervisors at end of ranked list get min_cap reduced first (toward 0).
```

### Safety net

- If after all adjustments `sum(max_caps) < student_count`: keep incrementally adding to max_caps
- If `sum(min_caps) > student_count`: keep reducing min_caps
- Final check: `min_caps[i] ≤ max_caps[i]` enforced for all i

---

## 8. Greedy Solver (`recommender.py::_solve_assignment`)

### Phase 1 — Initial argmax assignment

```python
assignment = np.argmax(score_matrix, axis=1)
# assignment[i] = index of supervisor with highest score for student i

counts = np.bincount(assignment, minlength=supervisor_count)
# counts[j] = how many students assigned to supervisor j
```

Example state after argmax (3 supervisors, max_cap=2):
```
assignment = [0, 1, 0, 0, 1, 2]    # students 0,2,3 → supervisor 0; 1,4 → sup 1; 5 → sup 2
counts     = [3, 2, 1]              # supervisor 0 is overfull (max=2)
```

### Phase 2 — Overflow fix loop

Runs up to `max_iter = N_students × N_supervisors × 10` iterations.

```
Each iteration:
  1. Find all overfull supervisors (count > max_cap)
  2. For every student in overfull supervisors:
       For every target supervisor with room (count < max_cap):
         penalty = score[student, current_supervisor] - score[student, target]
  3. Execute the move with the LOWEST penalty (least score sacrifice)
  4. Update assignment and counts
  5. Repeat until no overfull supervisors remain
```

Example:
```
supervisor 0 is overfull, has students [0, 2, 3]

scores for student 2:  [0.85, 0.73, 0.60]   current=0, penalty to move to 1 = 0.85-0.73 = 0.12
scores for student 3:  [0.80, 0.40, 0.71]   current=0, penalty to move to 1 = 0.80-0.40 = 0.40
                                              penalty to move to 2 = 0.80-0.71 = 0.09 ← LOWEST

→ Move student 3 from supervisor 0 to supervisor 2 (penalty 0.09)
counts = [2, 2, 2]   ← all within max_cap
```

### Phase 3 — Underflow fix loop

Same structure but for underfull supervisors:

```
Each iteration:
  1. Find all underfull supervisors (count < min_cap)
  2. Find "donor" supervisors (count > min_cap)
  3. For every student in a donor supervisor:
       penalty = score[student, donor] - score[student, underfull_target]
  4. Execute move with LOWEST penalty
  5. Repeat until no underfull supervisors remain
```

### Final validation

```python
if any(counts[j] < min_caps[j] or counts[j] > max_caps[j] for j in range(supervisor_count)):
    raise RuntimeError("Greedy fallback tidak menemukan solusi yang memenuhi kapasitas.")

objective = sum(score_matrix[i, assignment[i]] for i in range(student_count))
```

`objective` = sum of all final scores (what we tried to maximize across all assignments).

---

## 9. Evaluation Metrics (`app/evaluation.py`)

Ground truth: `student["current_supervisor_code"]` (the actual supervisor assigned in the real semester EPC mapping).

Metrics are computed **twice**:
- On `content_similarity_matrix` (pure cosine similarity)
- On `hybrid_score_matrix` (cosine + group bonus)

### For each student with a ground truth label

```python
row = score_matrix[student_idx]             # scores for this student vs all supervisors
ranked = np.argsort(-row)                   # descending rank order of supervisor indices
rank = position_of_true_supervisor + 1      # 1-indexed
```

### Per-student metric values

| Metric | Formula |
|--------|---------|
| Reciprocal Rank | `1.0 / rank` |
| Hit@1 | `1.0 if rank == 1 else 0.0` |
| Hit@5 | `1.0 if rank <= 5 else 0.0` |
| nDCG@5 | `1.0 / log2(rank + 1) if rank <= 5 else 0.0` |
| nDCG@10 | `1.0 / log2(rank + 1) if rank <= 10 else 0.0` |
| Precision@5 | `1.0 / 5.0 if rank <= 5 else 0.0` |

### Aggregation across all evaluated students

```python
MRR           = mean(reciprocal_ranks)
Hit@1         = mean(hit1_list)
Hit@5         = mean(hit5_list)
nDCG@5        = mean(ndcg5_list)
nDCG@10       = mean(ndcg10_list)
Avg Rank      = mean(ranks)
Avg Sim @1    = mean(score_of_top1_supervisor)
Avg True Sim  = mean(score_of_true_supervisor)
Precision@5   = mean(precision5_list)
```

### Assignment Match Rate (separate metric)

After the greedy solver assigns students to supervisors:
```python
match_rate = count(assigned_code == current_supervisor_code) / count(students with ground truth)
```

This measures how often the system replicates the real human assignment, not just retrieval rank.

### Random baseline (14 supervisors, for comparison)

| Metric | Random Baseline |
|--------|----------------|
| Hit@1 | 1/14 = 0.071 |
| Hit@5 | 5/14 = 0.357 |
| MRR | (1+0.5+0.33+...+1/14) / 14 ≈ 0.232 |
| nDCG@5 | (1 + 0.631 + 0.5 + 0.431 + 0.387) / 14 ≈ 0.211 |
| Avg Rank | (1+2+...+14) / 14 = 7.5 |

Your model's scores must clearly exceed these to justify the semantic similarity approach.

---

## 10. Embedding Methodologies Tried

### Available models (configurable per run)

| Model | Type | Notes |
|-------|------|-------|
| `intfloat/multilingual-e5-large-instruct` | Instruction-tuned multilingual | **Default**; supports `task=` parameter for task-specific embeddings |
| `BAAI/bge-m3` | Multilingual dense retrieval | Strong cross-lingual; no task prefix needed |
| `Qwen/Qwen3-Embedding-0.6B` | Lightweight multilingual | Smaller, faster |

All three are loaded via `sentence-transformers` with the same interface.

### Fallback chain (automatic, no configuration needed)

```
1. sentence-transformers model  →  dense 1024-dim vectors, matmul cosine
       ↓ (if load fails)
2. TF-IDF (sklearn, ngram 1-2)  →  sparse term-frequency vectors, cosine_similarity
       ↓ (if sklearn missing)
3. Jaccard token overlap         →  |A ∩ B| / |A ∪ B| on whitespace tokens
```

### Difference between methods

| Method | Cross-lingual | Synonyms ("magang" = "internship") | Speed |
|--------|--------------|-------------------------------------|-------|
| Sentence-Transformer | Yes | Yes | Slow (GPU recommended) |
| TF-IDF | No | No | Fast |
| Jaccard | No | No | Fast |

---

## 11. Label Taxonomy (19 categories)

Used in `detect_labels()` to tag both student documents and supervisor profiles.

| Label | Trigger Terms (sample) |
|-------|----------------------|
| `independent_study` | "specific independent study", "studi independent" |
| `internship` | "internship", "magang", "certified internship" |
| `research` | "research", "fellowship", "certified research" |
| `binus_bandung` | "binus bandung", "school of computer science bandung" |
| `binus_internal_internship` | "binus", "apple developer academy" *(composite — requires internship label also present)* |
| `network_cloud` | "network", "jaringan", "cloud", "infrastructure" |
| `entrepreneurship` | "entrepreneurship", "startup", "business", "venture" |
| `iot_embedded` | "iot", "embedded", "microcontroller", "sensor", "drone", "uav" |
| `government_public` | "government", "pemerintah", "kementerian", "dinas" |
| `health_medical` | "health", "kesehatan", "hospital", "medis" |
| `hospital_niche` | "hospital", "rumah sakit", "siloam", "rsud", "rsia" |
| `game_interactive` | "game", "games", "unity", "unreal" |
| `finance_banking` | "bank", "banking", "perbankan", "financial" |
| `apple_mobile` | "apple academy", "apple developer academy", "swift", "ios" |
| `web_fullstack` | "web", "frontend", "backend", "react", "javascript", "api" |
| `software_engineering` | "software", "application", "developer", "engineering", "system" |
| `data_ai` | "data science", "machine learning", "analytics", "ai", "model" |
| `cyber_security` | "security", "cyber", "infosec", "pentest" |
| `education` | "academy", "instructor", "sekolah", "learning", "kampus" |
| `general_flexible` | *(not detected — manually assigned in supervisor seed profiles)* |

Detection is substring-based (`any(term in text for term in terms)`), applied to the normalized document string.
