# Code Context: Faculty Supervisor Recommender System (English)

> Deep crawl of `/faculty-recommender-thesis` on 2026-06-07.
> Goal: document **all implemented features** in detail for thesis writing.

---

## 1. System Overview

A **Flask-based web application** that recommends thesis supervisors to students using **Content-Based Filtering** with semantic embeddings. The pipeline spans 10 stages from Excel import to database persistence and report export.

**Tech Stack:**
- Backend: Python 3.12, Flask 3.x
- ORM: SQLAlchemy 2.x (SQLite default, PostgreSQL/MySQL-ready)
- Embedding: SentenceTransformers (GPU/CPU) → TF-IDF → Token Overlap (fallback chain)
- Frontend: Jinja2 + plain HTML/CSS, minimal AJAX
- Export: pandas + openpyxl

---

## 2. Recommendation Pipeline (10 Stages)

### Stage 1 — Excel Import (`app/excel_io.py`)
- **Required columns (6):** STUDENT ID, TRACK, PARTNER/LECTURER, POSITION/TOPIC, WORK SCHEMA, GPA
- **Optional columns (11):** BINUSIAN ID, STUDENT NAME, EMAIL, PHONE, TOTAL SKS, DURATION, JOB START/END DATE, ENROLLMENT STATUS, kode dosen, FS
- Data cleaning: `_clean_id()`, `_clean_text()`, `_clean_float()`, `_clean_int()`, `_clean_date()` (handles Excel float artifact `"1234.0"`)
- Supports import from local file path (`read_students_from_excel_path`) and uploaded bytes (`read_students_from_excel_bytes`)
- Upsert semantics: update existing student by student_id or insert new

### Stage 2 — Document Building (`app/rules.py`)
- **`normalize_text(value)`**: lowercase + strip non-alphanumeric + collapse spaces → canonical text
- **`student_document(student)`**: join track + position_topic + work_schema → student embedding document
- **`profile_document(profile)`**: join supervisor keywords + labels → supervisor embedding document
- **19 Detected Domain Labels:**
  - `independent_study`, `internship`, `research`, `binus_bandung`, `binus_internal_internship`
  - `network_cloud`, `entrepreneurship`, `iot_embedded`, `government_public`, `health_medical`
  - `hospital_niche`, `game_interactive`, `finance_banking`, `apple_mobile`, `web_fullstack`
  - `software_engineering`, `data_ai`, `cyber_security`, `education`
- Composite label: `"internship"` + BINUS-internal keywords → adds `"binus_internal_internship"` + `"binus_bandung"`

### Stage 3 — Embedding Generation & Similarity (`app/embedding.py`)
**3-Tier Fallback Chain:**

1. **SentenceTransformer (primary):**
   - Default model: `intfloat/multilingual-e5-large-instruct`
   - Alternatives: `BAAI/bge-m3`, `Qwen/Qwen3-Embedding-0.6B`
   - Encoding with L2 normalization → cosine similarity via dot product (matmul)
   - Device resolution: `auto` → check CUDA availability → fallback to CPU
   - Thread-safe lazy loading (double-checked locking pattern)
   - Singleton provider per model name (`_PROVIDERS` dict cache)

2. **TF-IDF (fallback 1):** scikit-learn `TfidfVectorizer(ngram_range=(1,2))` + cosine_similarity
3. **Token Overlap (fallback 2):** Jaccard coefficient on token sets

**Key functions:**
- `get_embedding_provider(model_name)`: retrieve or create provider (singleton)
- `warmup_model(model_name)`: pre-load model in background thread
- `get_provider_statuses(model_names)`: per-model status: `not_loaded`, `loading`, `ready`, `fallback`

### Stage 4 — Hybrid Scoring & Company Group Bonus (`app/recommender.py`)

**Score Matrix Assembly:**
```
weighted_similarity = similarity_matrix × SIMILARITY_WEIGHT   (default 1.0)
score_matrix = weighted_similarity + group_bonus (if enabled)
```

**Company Group Bonus (`_apply_company_group_bonus`):**
- Group students by normalized `partner_lecturer` (company name)
- Bonus conditions (all must be met):
  - At least 2 students from the same company
  - Topic diversity ≤ 6 distinct position_topic tokens
  - Score margin between best and 2nd-best supervisor ≥ 0.08
- Bonus formula: `effective_bonus = COMPANY_GROUP_BONUS / max(1.0, log2(group_size + 1))`
- Rationale: larger groups get smaller per-student bonus (logarithmic diminishing returns)

### Stage 5 — Capacity Planning (`_build_capacity_plan`)
- Base: `target_min=10`, `target_max=12` students per supervisor
- **Overflow** (total students > sum of max_caps): add slots to `CAPACITY_PRIORITY_CODES` first, then round-robin all
- **Underflow** (total students < sum of min_caps): reduce min from lowest-priority supervisors first
- Safety invariants: `sum(max) ≥ N`, `sum(min) ≤ N`, `min[j] ≤ max[j]`
- **4 Priority Supervisors (`CAPACITY_PRIORITY_CODES`):**
  - D2211 (Dr. Abdul Haris Rangkuti), D6184 (Dr. Mochammad Haldi Widianto)
  - D6826 (Karen Etania Saputra), D1749 (Dr. Johan Muliadi Kerta)

### Stage 6 — Greedy Assignment Solver (`_solve_assignment`)
**Two-Phase Greedy Algorithm:**

1. **Init:** `assignment[i] = argmax(score_matrix[i, :])` — each student assigned to highest-scoring supervisor
2. **Phase 1 — Reduce Overfull:** move students from over-capacity supervisors; pick move with *minimum score penalty*
3. **Phase 2 — Fill Underfull:** move students from donors (count > min) to under-capacity targets; pick *minimum score penalty*

- Max iterations: `N × M × 10`
- Objective = sum of all `score_matrix[i, assignment[i]]`
- Final validation: raises RuntimeError if `min ≤ count ≤ max` violated for any supervisor

### Stage 7 — Evaluation (`app/evaluation.py`)
**Three Evaluation Passes:**

| Pass | Matrix | Description |
|------|--------|-------------|
| `content_based` | `content_similarity_matrix` | Semantic embeddings only |
| `hybrid_score` | `hybrid_score_matrix` | Embeddings + group bonus |
| `assignment_match` | Solver output | End-to-end assignment accuracy |

**Retrieval Metrics** (ground truth = `current_supervisor_code`):

| Metric | Formula | Meaning |
|--------|---------|---------|
| **MRR** | Σ(1/rank_i) / N | Mean inverse rank of ground truth supervisor |
| **Hit@1** | Count(rank≤1) / N | Ground truth is top-1 recommendation |
| **Hit@5** | Count(rank≤5) / N | Ground truth in top-5 |
| **NDCG@5** | Mean(1/log₂(rank+1)) if rank≤5 | Logarithmically discounted ranking quality |
| **NDCG@10** | Mean(1/log₂(rank+1)) if rank≤10 | Same, cutoff 10 |
| **Precision@5** | Count(rank≤5) / (N×5) | Top-5 precision |
| **Avg Rank** | Mean(rank_i) | Average position of ground truth |
| **Match Rate** | matched / evaluated | Fraction matching current supervisor assignments |

- Students without `current_supervisor_code` excluded from IR metrics
- All floats rounded to 6 decimal places

### Stage 8 — Database Persistence (`app/services.py`)
`generate_and_store_recommendations()` stores:
- `recommendation_runs` record: input_source, total_students, total_supervisors, capacity metadata, solver info, embedding info, evaluation_json, pipeline_config_json, objective_score, rankings_json
- `recommendations` records (N rows, one per student): run_id, student_id, supervisor_id, similarity_score, group_boost, final_score, rule_matches, company_group_key

### Stage 9 — Rankings JSON (`_build_rankings_json`)
Per student, stores top-5 supervisor candidates:
```json
{
  "student_id": "...",
  "candidates": [
    {
      "rank": 1, "supervisor_code": "...", "similarity_score": 0.85,
      "group_boost": 0.05, "final_score": 0.90,
      "is_assigned": true, "score_delta_from_rank1": 0.0
    }
  ]
}
```
- Assigned supervisor always included even if outside top-5 (actual_rank tracked)

### Stage 10 — Excel Export
**Standard Export** (`rekomendasi_dosen_run_{id}.xlsx`, 3 sheets):
- `recommendations`: full assignment with all scores
- `summary`: per-supervisor capacity (assigned, min, max, within_capacity)
- `evaluation`: all evaluation metrics as (section, metric, value) rows

**Detailed Export** (`rekomendasi_dosen_run_{id}_detailed.xlsx`, 4 sheets):
- Adds `rankings` sheet: all top-N candidates per student, one row per candidate

**Supervisor Config Export** (`supervisor_config_export.xlsx`, 2 sheets):
- `supervisor_config`: supervisor list (id, code, name, keywords, is_active)
- `track_reference`: distinct student tracks in database

---

## 3. Database Schema

### `supervisors` Table
| Column | Type | Notes |
|--------|------|-------|
| id | Integer PK | - |
| code | String(16) Unique | Faculty code (e.g., D2211) |
| name | String(255) Unique | Full name |
| profile_keywords | Text | Competency keywords (comma-separated) |
| is_active | Boolean | Active status |

### `students` Table
| Column | Type | Notes |
|--------|------|-------|
| student_id | String(32) Unique | Student ID (NIM) |
| binusian_id | String(32) | Alternative BINUS ID |
| name, email, phone | String | Contact info |
| track | String(255) | Study program/track |
| gpa | Float | Grade Point Average |
| total_sks | Integer | Total credit hours |
| partner_lecturer | String(255) | Company/partner lecturer name |
| position_topic | Text | Internship position & thesis topic |
| work_schema | String(64) | Work arrangement type |
| current_supervisor_code | String(16) | Current supervisor (ground truth label) |

### `recommendation_runs` Table
- Stores full pipeline configuration, evaluation metrics, capacity bounds, and rankings as JSON columns
- One-to-Many relationship with `recommendations`

### `recommendations` Table
- Unique constraint: `(run_id, student_id)`
- Stores: similarity_score, group_boost, final_score, rule_matches, company_group_key

### `app_users` Table
- Authentication: username (min 3 chars), password_hash (Werkzeug PBKDF2), full_name, created_at

---

## 4. Supervisor Management Features (`/supervisors`)

### Keywords Studio (UI)
- HTML5 datalist-based autocomplete input with suggestions from existing keywords
- Token addition via "Add" button or pressing Enter/Comma keys
- Normalization: trim + collapse whitespace, case-insensitive duplicate prevention
- Chip display with 6 rotating CSS color classes (k0–k5)
- Storage: comma-separated string in `profile_keywords` DB column

### Adaptive Profile Enrichment (`_supervisor_profiles_from_db`)
- System **automatically learns** from historical student assignments:
  - Fetches students previously assigned to this supervisor (via `current_supervisor_code`)
  - Extracts tokens from student documents, filters stopwords + tokens < 3 chars
  - Takes labels with frequency ≥ 2 (top 8 labels)
  - Merges with manual keywords (top 16 tokens, frequency ≥ 2)
- Result: `SupervisorProfile` with keywords + labels adaptively enriched from assignment history

---

## 5. Authentication Features

- Registration: username (min 3 chars normalized), password (min 6 chars), confirm password, duplicate check
- Login: username normalization (lowercase), Werkzeug password hash verification
- Session-based: `session["user_id"]`
- `@login_required` decorator on all protected routes
- Safe redirect via `_safe_next()` (prevents open redirect vulnerabilities)

---

## 6. All HTTP Routes

| Route | Method | Feature |
|-------|--------|---------|
| `/register`, `/login` | GET, POST | Authentication |
| `/logout` | POST | Clear session |
| `/dashboard` | GET | KPI metrics + trend chart + latest run snapshot |
| `/data` | GET | Health check, import trigger |
| `/data/import-default` | POST | Import from default Excel file |
| `/data/import-upload` | POST | Upload & import new Excel file |
| `/generate` | POST | Execute recommendation pipeline |
| `/runs` | GET | All run history (limit 250) |
| `/runs/<id>` | GET | Run detail: metrics, capacity, mismatch spotlight |
| `/runs/<id>/recommendations` | GET | Recommendation table + filters (q, supervisor, mismatch) |
| `/runs/<id>/export` | GET | Download standard Excel |
| `/runs/<id>/export/detailed` | GET | Download Excel with rankings sheet |
| `/supervisors` | GET | Supervisor roster + Keywords Studio |
| `/supervisors/add` | POST | Add/update supervisor |
| `/supervisors/keywords/update` | POST | Save supervisor keywords |
| `/supervisors/export` | GET | Export supervisor configuration |
| `/api/model-status` | GET | Embedding model status (JSON) |
| `/api/supervisors` | GET | Active supervisor list (JSON) |
| `/benchmark` | GET | Placeholder page (not yet implemented) |

---

## 7. System Configuration (Environment Variables)

| Variable | Default | Description |
|----------|---------|-------------|
| `DATABASE_URL` | `sqlite:///recommendation.db` | DB connection URL |
| `DEFAULT_EXCEL_PATH` | `map_2026.xlsx` | Student input file |
| `DEFAULT_SHEET_NAME` | `Sheet1` | Excel sheet name |
| `TARGET_MIN_CAPACITY` | `10` | Min students per supervisor |
| `TARGET_MAX_CAPACITY` | `12` | Max students per supervisor |
| `SIMILARITY_WEIGHT` | `1.0` | Similarity score multiplier |
| `COMPANY_GROUP_BONUS` | `0.2` | Company cohort bonus base value |
| `HIGH_GPA_THRESHOLD` | `3.8` | High GPA threshold |
| `EMBEDDING_MODEL_NAME` | `intfloat/multilingual-e5-large-instruct` | Default embedding model |
| `EMBEDDING_DEVICE` | `auto` | Compute device (auto/cuda/cpu) |

**Available Embedding Models:**
1. `BAAI/bge-m3` — BGE multilingual model
2. `Qwen/Qwen3-Embedding-0.6B` — Lightweight Qwen
3. `intfloat/multilingual-e5-large-instruct` — E5 large multilingual **(DEFAULT)**

---

## 8. Algorithm Thresholds & Parameters

| Parameter | Value | Location | Meaning |
|-----------|-------|----------|---------|
| Min group size (bonus) | 2 | `recommender.py` | Minimum 2 same-company students for group bonus |
| Max topic diversity | 6 | `recommender.py` | Skip bonus if > 6 distinct topic tokens |
| Min supervisor margin | 0.08 | `recommender.py` | Skip bonus if top-2 supervisors differ < 0.08 |
| Bonus decay base | log₂(N+1) | `recommender.py` | Larger groups get smaller per-student bonus |
| Max solver iterations | N × M × 10 | `recommender.py` | Per-phase greedy iteration limit |
| Profile token threshold | ≥ 2 | `services.py` | Min frequency for adaptive token to enter profile |
| Max learned keywords | 16 | `services.py` | Top-N adaptive keywords per supervisor |
| Max learned labels | 8 | `services.py` | Top-N adaptive labels per supervisor |

---

## 9. Key Data Structures (from `app/schemas.py`)

### `SupervisorProfile` (frozen dataclass)
```python
code: str           # Supervisor code
name: str           # Full name
keywords: tuple     # Competency keywords
labels: tuple       # Domain labels
```

### `RecommendationOutput`
```python
items: list[RecommendationItem]
counts_by_supervisor: dict[str, int]
solver_name: str                      # "greedy"
objective_score: float
capacity_plan: CapacityPlan
embedding_backend: str                # "sentence-transformers", "tfidf-fallback", etc.
embedding_model: str
supervisor_codes: list[str]           # Alphabetically sorted
content_similarity_matrix: np.ndarray  # Shape: (N_students, M_supervisors)
hybrid_score_matrix: np.ndarray        # After bonuses applied
```

### `RunOverrides` (pipeline configuration)
```python
embedding_model: str
embedding_task: str
enable_group_bonus: bool
enable_extra_docs: bool
capacity_priority_codes: list[str]
target_min_capacity: int
target_max_capacity: int
```

### `CapacityPlan`
```python
min_caps: list[int]   # Per-supervisor minimum (may be relaxed)
max_caps: list[int]   # Per-supervisor maximum (may be relaxed)
relaxed: bool         # Whether any constraint was adjusted
note: str | None      # Human-readable adjustment explanation
```

---

## 10. Project File Structure

```
faculty-recommender-thesis/
├── flask_app.py          # Entry point: routes + auth + @login_required
├── seed.py               # CLI: init DB + seed supervisor data
├── requirements.txt      # Python dependencies
├── Dockerfile / docker-compose.yml
├── app/
│   ├── config.py         # All constants & env vars
│   ├── models.py         # SQLAlchemy ORM: supervisors, students, runs, recommendations, users
│   ├── schemas.py        # Dataclasses: SupervisorProfile, RecommendationOutput, RunOverrides, etc.
│   ├── database.py       # Engine + SessionLocal factory
│   ├── queries.py        # All ORM queries (no raw SQL)
│   ├── services.py       # Pipeline orchestration + import/export services
│   ├── recommender.py    # Core: scoring + capacity planning + greedy solver
│   ├── embedding.py      # Multi-backend embedding provider with 3-tier fallback
│   ├── rules.py          # Text normalization + label detection + document builders
│   ├── evaluation.py     # IR metrics: MRR, NDCG, Hit@K, Precision@K, match rate
│   └── excel_io.py       # Excel import/export (pandas + openpyxl)
├── datasets/
│   ├── map_loader.py     # Excel loader → extra_supervisor_docs dict
│   └── seed_dataset/     # Seed data: supervisor profiles, label_terms, stopwords, seeder
├── templates/            # Jinja2 HTML templates
│   ├── dashboard.html    # KPI cards + trend bar chart
│   ├── data_center.html  # Import + pipeline trigger modal
│   ├── supervisors.html  # Keywords Studio with chip UI
│   ├── recommendations.html # Assignment table with server-side filtering
│   ├── runs.html         # Run history table
│   ├── run_detail.html   # Run analysis: metrics + capacity + mismatch spotlight
│   └── benchmark.html    # Stub placeholder
└── static/style.css      # Custom CSS (no Bootstrap/Tailwind)
```

---

## 11. UI Features Summary

### Dashboard (`/dashboard`)
- 4 KPI cards: Total Students, Active Supervisors, Total Runs, Latest Match Rate
- Horizontal bar chart: Match Rate + Hybrid MRR + Content MRR trend across recent runs
- Latest Run Snapshot: configuration badges (Group Bonus ON/OFF, Extra Docs ON/OFF), action buttons
- Recent Runs table (12 rows) with inline View links

### Data Center (`/data`)
- 4 KPI cards (students, labeled supervisors ratio, active supervisors, total runs)
- Default source file health check with progress bar for label coverage
- "Generate New Run" button opening run config modal
- Excel upload form

### Supervisors (`/supervisors`)
- Searchable supervisor selector (HTML5 datalist)
- Add Supervisor modal (code, name, keywords)
- Keywords Studio: keyword chip management with JS IIFE token logic
- Supervisor table with active status badges
- Keywords Detail modal (read-only chip display + edit link)

### Run Detail (`/runs/<id>`)
- Metrics grid: Run ID, Objective Score, Student/Supervisor counts, Mismatch Count
- Pipeline Configuration section (group bonus, extra docs, similarity weight)
- Capacity Note and Solver/Embedding Note (pre-formatted text)
- Evaluation table (nested section/metric/value from evaluation_json)
- Capacity Summary table (color-coded: OK green / Check red per supervisor)
- Mismatch Spotlight table (top 10 mismatched assignments)

### Recommendations (`/runs/<id>/recommendations`)
- Server-side filtering: text search, supervisor filter dropdown, mismatch-only toggle
- Table: Student ID, Name, Track, Partner, Topic, Current Supervisor, Recommended Supervisor, Final Score, Rules
- Row highlight for mismatch rows (CSS `.bad` class)
