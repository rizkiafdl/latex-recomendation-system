# 2026-06-07 — Batch Analysis: Python Methodology

**Source directory:** `output/batch_20260607_163454/`
**Files analyzed:** 18 `.xlsx` files — `rekomendasi_dosen_run_8_detailed.xlsx` through `rekomendasi_dosen_run_25_detailed.xlsx`

Each file has 5 sheets: `recommendations`, `rankings`, `summary`, `config`, `evaluation`.

---

## Agent 1 — Evaluation Metrics Analysis

**Sheets read:** `config` + `evaluation`

### Steps

1. For each of the 18 files, read the `config` sheet (key/value pairs) and the `evaluation` sheet (section, metric, value).
2. From `config`, extract: `embedding_model`, `group_bonus`, `extra_docs`, `capacity_priority_codes`.
3. From `evaluation`, pivot all metric rows into columns: `MRR`, `Hit@1`, `Hit@5`, `nDCG@5`, `nDCG@10`, `avg_rank`, `Precision@5`, `match_rate`, `matched`.
4. Assemble one row per run into a master comparison DataFrame (18 rows × config + metric columns).
5. Computed group-by aggregations:
   - Mean metrics by `embedding_model` (3 groups)
   - Mean metrics by `extra_docs` (True/False)
   - Mean metrics by `group_bonus` (True/False)
   - Mean metrics by whether `capacity_priority_codes` is empty or not
6. Identified best and worst run per metric using `idxmax` / `idxmin`.

---

## Agent 2 — Supervisor Load Distribution Analysis

**Sheets read:** `config` + `summary`

### Steps

1. For each file, read the `config` sheet and the `summary` sheet.
2. From `summary`, extract per-supervisor columns (supervisor_code, assigned count, capacity).
3. For each run computed aggregate distribution stats:
   - `min`, `max`, `mean`, `std` of assigned counts across all supervisors
   - `range` (max − min)
   - Count of supervisors at max capacity (13) vs. at min (12)
   - Gini coefficient of assignment counts
4. Compared stats across all 18 runs to identify if any config changes distribution balance.
5. Separately compared which supervisors get the extra student (count = 13) between `cap_priority=[]` and `cap_priority=CAPACITY_PRIORITY_CODES` runs.

---

## Agent 3 — Rankings Score Distribution Analysis

**Sheets read:** `config` + `rankings`

### Steps

1. For each file, read the `config` sheet and the `rankings` sheet (columns: student_id, student_name, rank, supervisor_code, supervisor_name, similarity_score, group_boost, final_score, is_assigned, score_delta_from_rank1).
2. Filtered to **assigned students only** (`is_assigned == True`).
3. Per run computed:
   - `sim_mean`, `sim_std` — mean and std of `similarity_score`
   - `fin_mean` — mean of `final_score`
   - `gap(fin-sim)` — mean difference between `final_score` and `similarity_score` (measures group_boost contribution)
   - `delta_mean` — mean `score_delta_from_rank1` (how far below rank-1 each assigned student is)
   - `%rank-1` — percentage of students assigned to their rank-1 supervisor
   - `%top-3` — percentage assigned within top-3
   - `mean_rank` — mean assigned rank
   - Count of students with `score_delta_from_rank1 > 0.15` (high displacement)
4. Group-by aggregations by model, `extra_docs`, `group_bonus` to isolate each dimension's effect.
5. Pooled rank distribution across all 18 runs to reveal bimodal assignment pattern.
