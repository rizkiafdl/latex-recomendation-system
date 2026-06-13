# Class Diagram Description — Faculty Supervisor Recommender

The system is organized into 4 groups: ORM Models, Runtime Dataclasses, one real Service Class, and 5 Utility Modules
(represented as «utility» or «repository» stereotype classes since Python uses module-level functions).

---

## Group 1: ORM Models (`app/models.py`)

These are pure data containers — no meaningful domain methods, only SQLAlchemy-managed relationships.

### Supervisor
- **Attributes:** id: int, code: str, name: str, profile_keywords: str, is_active: bool
- **Relationships:** recommendations: list[Recommendation]

### Student
- **Attributes:** id: int, student_id: str, binusian_id: str, name: str, email: str, phone: str, track: str, gpa: float,
  total_sks: int, partner_lecturer: str, position_topic: str, duration: str, job_start_date: str, job_end_date: str,
  work_schema: str, enrollment_status: str, current_supervisor_code: str, current_supervisor_name: str, created_at: datetime, updated_at: datetime
- **Relationships:** recommendations: list[Recommendation]

### RecommendationRun
- **Attributes:** id: int, created_at: datetime, created_by_id: int (FK → app_users), input_source: str, total_students: int, total_supervisors: int,
  target_min_capacity: int, target_max_capacity: int, capacity_relaxed: bool, capacity_note: str, capacity_bounds_json: str,
  solver_name: str, solver_note: str, embedding_backend: str, embedding_model: str, evaluation_json: str,
  pipeline_config_json: str, objective_score: float, rankings_json: str
- **Relationships:** recommendations: list[Recommendation] (cascade delete)

### Recommendation
- **Attributes:** id: int, run_id: int, student_id: int, supervisor_id: int, similarity_score: float, group_boost: float,
  final_score: float, rule_matches: str, company_group_key: str
- **Relationships:** run: RecommendationRun, student: Student, supervisor: Supervisor

### AppUser
- **Attributes:** id: int, username: str, full_name: str, password_hash: str, created_at: datetime
- No domain methods

---

## Group 2: Runtime Dataclasses (`app/schemas.py`)

Frozen / plain dataclasses — no methods, pure value objects used during the pipeline.

### SupervisorProfile `«frozen»`
- code: str, name: str, keywords: tuple[str], labels: tuple[str]

### RunOverrides
- embedding_model: str, embedding_task: str, enable_group_bonus: bool, enable_extra_docs: bool,
  capacity_priority_codes: list[str], target_min_capacity: int, target_max_capacity: int
  *(Note: `enable_rule_boost` removed — rule-based scoring is no longer in the pipeline)*

### CapacityPlan
- min_caps: list[int], max_caps: list[int], relaxed: bool, note: str

### RecommendationItem
- student: dict, supervisor: SupervisorProfile, similarity_score: float, group_boost: float, final_score: float,
  rule_matches: list[str], company_group_key: str
  *(Note: `rule_boost` removed)*

### RecommendationOutput
- items: list[RecommendationItem], counts_by_supervisor: dict[str, int], solver_name: str, objective_score: float,
  capacity_plan: CapacityPlan, solver_note: str, embedding_backend: str, embedding_model: str, embedding_note: str,
  supervisor_codes: list[str], content_similarity_matrix: ndarray, hybrid_score_matrix: ndarray

### EmbeddingInfo
- backend: str, model_name: str, note: str

---

## Group 3: Service Class (`app/embedding.py`)

The only real class with meaningful instance state and methods.

### EmbeddingProvider `«singleton per model»`
- **Attributes:** `_model_name: str`, `_lock: Lock`, `_loaded: bool`, `_model: SentenceTransformer`, `_info: EmbeddingInfo`
- **Methods:**
  - `+ info: EmbeddingInfo` (property)
  - `+ encode_batch(texts: list[str], task: str) → ndarray | None`
  - `+ similarity_matrix(source_docs: list[str], target_docs: list[str], task: str) → ndarray`
  - `- _ensure_loaded() → None`
  - `- _load_sentence_transformer() → None`

---

## Group 4: Utility Modules

Represented as «utility» stereotype classes — implemented as Python module-level functions.

### «utility» DocumentBuilder (`app/rules.py`)
- `+ normalize_text(value: object) → str`
- `+ detect_labels(text: str) → set[str]`
- `+ student_document(student: dict) → str`
- `+ profile_document(profile: SupervisorProfile) → str`
- `+ student_labels(student: dict) → set[str]`

### «utility» ExcelIO (`app/excel_io.py`)
- `+ read_students_from_excel_path(path: Path, sheet_name: str) → list[dict]`
- `+ read_students_from_excel_bytes(content: bytes, sheet_name: str) → list[dict]`

### «utility» Evaluator (`app/evaluation.py`)
- `+ evaluate_retrieval_metrics(score_matrix: ndarray, students: list, supervisor_codes: list) → dict`
- `+ evaluate_assignment_match_rate(items: list[RecommendationItem]) → dict`
- `+ build_evaluation_payload(content_scores, hybrid_scores, students, supervisor_codes, items) → dict`

### «utility» RecommendationEngine (`app/recommender.py`)
- `+ generate_recommendations(students, supervisor_profiles, overrides, extra_supervisor_docs) → RecommendationOutput`
- `- _build_capacity_plan(supervisor_codes, student_count, priority_codes, target_min, target_max) → CapacityPlan`
- `- _solve_assignment(score_matrix: ndarray, min_caps, max_caps) → (ndarray, float)`
- `- _apply_company_group_bonus(score_matrix: ndarray, students: list) → (ndarray, dict)`

### «repository» QueryRepository (`app/queries.py`)
- `+ get_user_by_id(session, user_id) → AppUser`
- `+ get_user_by_username(session, username) → AppUser`
- `+ get_active_supervisors_ordered(session) → list[Supervisor]`
- `+ get_supervisor_by_code(session, code) → Supervisor`
- `+ get_active_supervisor_map(session) → dict[str, Supervisor]`
- `+ get_all_students(session) → list[Student]`
- `+ get_students_by_supervisor_codes(session, codes) → list[Student]`
- `+ get_latest_run(session) → RecommendationRun`
- `+ list_runs(session, limit) → list[RecommendationRun]`
- `+ get_run_by_id(session, run_id) → RecommendationRun`
- `+ get_recommendations_with_entities(session, run_id) → list[tuple]`
- `+ get_supervisor_recommendation_counts(session, run_id) → list[tuple]`

### «service» RecommenderService (`app/services.py`) — orchestrator
- `+ init_db() → None`
- `+ register_user(session, username, password, full_name) → AppUser`
- `+ authenticate_user(session, username, password) → AppUser`
- `+ import_students_from_path(session, excel_path, sheet_name) → dict`
- `+ import_students_from_bytes(session, content, filename, sheet_name) → dict`
- `+ add_or_update_supervisor(session, code, name, keywords) → Supervisor`
- `+ update_supervisor_keywords(session, code, keywords) → None`
- `+ list_supervisor_profiles_for_web(session) → list[dict]`
- `+ generate_and_store_recommendations(session, overrides, input_source) → RecommendationRun`
- `+ list_recommendations(session, run_id) → (RecommendationRun, list[dict])`
- `+ summary_by_supervisor(session, run_id) → (RecommendationRun, list[dict])`
- `+ evaluation_by_run(session, run_id) → (RecommendationRun, dict)`
- `+ export_recommendations_excel(session, run_id) → (bytes, str)`
- `+ export_recommendations_excel_detailed(session, run_id) → (bytes, str)`
- `+ export_supervisor_configuration_excel(session) → (bytes, str)`

---

## Relationships

### ORM Associations (solid lines with multiplicities)
- AppUser `1` → `*` RecommendationRun (created_by_id FK)
- RecommendationRun `1` → `*` Recommendation (cascade delete)
- Recommendation `*` → `1` Student
- Recommendation `*` → `1` Supervisor

### Runtime Object Composition (solid lines)
- RecommendationOutput `1` ◆→ `*` RecommendationItem
- RecommendationOutput `1` ◆→ `1` CapacityPlan
- RecommendationItem `*` → `1` SupervisorProfile
- EmbeddingProvider `1` ◆→ `1` EmbeddingInfo

### Dependency / Uses (dashed arrows)
- RecommenderService --uses--> RecommendationEngine (calls generate_recommendations)
- RecommenderService --uses--> QueryRepository (all DB reads)
- RecommenderService --uses--> ExcelIO (student import)
- RecommenderService --uses--> Evaluator (calls build_evaluation_payload)
- RecommendationEngine --uses--> EmbeddingProvider (calls encode_batch)
- RecommendationEngine --uses--> DocumentBuilder (calls student_document, profile_document)
- RunOverrides --input to--> RecommendationEngine
- RecommendationEngine --returns--> RecommendationOutput
- RecommenderService --produces--> RecommendationRun (persists to DB)

### Cross-Layer Notes
- SupervisorProfile is sourced from Supervisor (ORM) by RecommenderService
- RecommendationOutput is persisted as RecommendationRun + Recommendation[] by RecommenderService
- AppUser is standalone — used only by auth methods in RecommenderService, no pipeline involvement