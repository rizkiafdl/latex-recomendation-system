# Agent 3 — Rankings Score Distribution Analysis

**Runs analyzed:** 26–43 (18 files)  
**Date:** 2026-06-09  
**Scope:** Assigned students only (is_assigned=True), 168 students per run.

---

## 1. Per-Run Score and Assignment Quality Stats

| Run | Model | gb | edocs | cap | sim_mean | fin_mean | gap(fin-sim) | delta_mean | %rank1 | %top3 | mean_rank | n_disp>0.15 |
|-----|-------|----|-------|-----|----------|----------|--------------|------------|--------|-------|-----------|-------------|
| 26 | bge-m3 | F | T | — | 0.6447 | 0.6447 | 0.0000 | 0.0297 | **67.8%** | 85.4% | 2.45 | 15 |
| 27 | bge-m3 | F | T | ✓ | 0.6444 | 0.6444 | 0.0000 | 0.0300 | 67.8% | 85.4% | 2.45 | 15 |
| 28 | bge-m3 | T | F | — | 0.6296 | 0.6443 | 0.0147 | 0.0228 | 62.0% | **86.5%** | **2.35** | 8 |
| 29 | bge-m3 | T | F | ✓ | 0.6290 | 0.6437 | 0.0147 | 0.0234 | 62.6% | 85.4% | 2.37 | 8 |
| 30 | bge-m3 | F | F | — | 0.6296 | 0.6296 | 0.0000 | 0.0228 | 62.0% | 86.5% | 2.35 | 8 |
| 31 | bge-m3 | F | F | ✓ | 0.6290 | 0.6290 | 0.0000 | 0.0234 | 62.6% | 85.4% | 2.37 | 8 |
| 32 | qwen3 | F | T | — | 0.6838 | 0.6838 | 0.0000 | 0.0417 | 49.1% | 83.6% | 2.86 | 15 |
| 33 | qwen3 | F | T | ✓ | 0.6851 | 0.6851 | 0.0000 | 0.0404 | 49.7% | 83.6% | 2.82 | 15 |
| 34 | qwen3 | T | F | — | 0.7072 | 0.7172 | 0.0100 | 0.0437 | 50.9% | 70.2% | 2.93 | 14 |
| 35 | qwen3 | T | F | ✓ | 0.7075 | 0.7175 | 0.0100 | 0.0434 | 51.5% | 70.2% | 2.89 | 15 |
| 36 | qwen3 | F | F | — | 0.7072 | 0.7072 | 0.0000 | 0.0437 | 50.9% | 70.2% | 2.93 | 14 |
| 37 | qwen3 | F | F | ✓ | 0.7075 | 0.7075 | 0.0000 | 0.0434 | 51.5% | 70.2% | 2.89 | 15 |
| 38 | me5-large | F | T | — | 0.8927 | 0.8927 | 0.0000 | 0.0142 | 54.4% | 85.4% | 2.71 | **0** |
| 39 | me5-large | F | T | ✓ | 0.8924 | 0.8924 | 0.0000 | 0.0145 | 54.4% | 84.8% | 2.74 | **0** |
| 40 | me5-large | T | F | — | 0.8959 | 0.8959 | 0.0000 | 0.0120 | 60.8% | 83.0% | 2.91 | **0** |
| 41 | me5-large | T | F | ✓ | 0.8960 | 0.8960 | 0.0000 | 0.0119 | 60.8% | 82.5% | 2.89 | **0** |
| 42 | me5-large | F | F | — | 0.8959 | 0.8959 | 0.0000 | 0.0120 | 60.8% | 83.0% | 2.91 | **0** |
| 43 | me5-large | F | F | ✓ | 0.8960 | 0.8960 | 0.0000 | 0.0119 | 60.8% | 82.5% | 2.89 | **0** |

> **gb** = group_bonus, **edocs** = extra_docs, **delta_mean** = mean score gap from rank-1, **n_disp>0.15** = students assigned >0.15 below their rank-1 score.

---

## 2. Group-by Aggregations

### By Embedding Model

| Model | sim_mean | fin_mean | gap(fin-sim) | delta_mean | %rank1 | %top3 | mean_rank |
|-------|----------|----------|--------------|------------|--------|-------|-----------|
| **bge-m3** | 0.634 | 0.639 | 0.005 | 0.025 | **64.1%** | **85.8%** | **2.39** |
| me5-large | 0.895 | 0.895 | 0.000 | **0.013** | 58.7% | 83.5% | 2.84 |
| qwen3-0.6b | 0.700 | 0.703 | 0.003 | 0.043 | 50.6% | 74.7% | 2.89 |

### By `extra_docs`

| extra_docs | sim_mean | fin_mean | delta_mean | %rank1 | %top3 | mean_rank |
|-----------|----------|----------|------------|--------|-------|-----------|
| **True** | 0.741 | 0.741 | 0.028 | **57.2%** | **84.7%** | **2.67** |
| False | 0.744 | 0.748 | 0.026 | 58.1% | 79.6% | 2.72 |

> extra_docs=True improves top-3 reach (+5.1%) and mean rank despite a marginal drop in raw %rank1.

### By `group_bonus`

| group_bonus | sim_mean | fin_mean | gap(fin-sim) | delta_mean | %rank1 | %top3 | mean_rank |
|------------|----------|----------|--------------|------------|--------|-------|-----------|
| False | 0.742 | 0.742 | 0.000 | 0.027 | 57.65% | 82.2% | 2.70 |
| True | 0.744 | 0.752 | **0.008** | 0.026 | 58.1% | 79.6% | 2.72 |

> group_bonus adds a measurable `gap(fin-sim)` = +0.008 — confirming the boost is applied — but has minimal effect on %rank1 and %top3 because the boost doesn't change most students' assigned rank.

### By `cap_priority`

| cap_priority | sim_mean | fin_mean | delta_mean | %rank1 | %top3 | mean_rank |
|-------------|----------|----------|------------|--------|-------|-----------|
| False | 0.743 | 0.746 | 0.027 | 57.6% | 81.5% | 2.71 |
| True | 0.743 | 0.746 | 0.027 | 58.0% | 81.1% | 2.70 |

> cap_priority has near-zero effect on score distributions — as expected since it influences slot assignment order, not scores.

---

## 3. Pooled Rank Distribution (all 18 runs, 3,078 assigned students)

| Assigned Rank | Count | Share |
|--------------|-------|-------|
| Rank 1 | 1,779 | **57.8%** |
| Rank 2 | 411 | 13.4% |
| Rank 3 | 313 | 10.2% |
| Rank 4 | 147 | 4.8% |
| Rank 5 | 76 | 2.5% |
| Rank > 5 | 352 | **11.4%** |

The distribution is **strongly bimodal**: 57.8% of students are assigned to their top-ranked supervisor, but 11.4% are displaced beyond rank 5. The middle ranks (2–5) absorb 30.9% of students, reflecting capacity constraints pushing students down the list when their preferred supervisor is full.

---

## 4. Best and Worst Runs by Assignment Quality

| Metric | Best Run | Value | Worst Run | Value |
|--------|----------|-------|-----------|-------|
| %rank1 (highest) | Run 26 (bge-m3, edocs=T) | **67.8%** | Run 32 (qwen3, edocs=T) | 49.1% |
| %rank1 (lowest) | Run 32 | 49.1% | — | — |
| mean_rank (lowest=best) | Run 28/30 (bge-m3) | **2.35** | Run 34/36 (qwen3) | 2.93 |
| delta_mean (lowest=best) | Run 40–43 (me5-large) | **0.012** | Run 34–37 (qwen3) | 0.044 |
| n_disp>0.15 (fewest) | Run 38–43 (me5-large) | **0** | Run 26/27/32/33 | 15 |

---

## 5. Key Insights

- **bge-m3 delivers the best assignment quality.** 64.1% of students are assigned to rank-1 (their most similar supervisor), mean_rank=2.39 — both the best figures among the three models. Its lower absolute similarity scores are more **discriminative**, which helps the slot allocator fill high-preference matches first.
- **me5-large has the tightest score spread** (delta_mean=0.013, zero high-displacement students). Its scores cluster very closely together — no student is pushed far from their best match, but fewer reach rank-1 because the small inter-score gaps make ties more frequent.
- **qwen3-0.6b is the weakest model for assignment quality.** Only 50.6% of students reach rank-1 and it has the highest displacement (delta_mean=0.043, up to 15 students per run displaced >0.15 from rank-1). Its scores are relatively high in absolute value but not discriminative enough.
- **extra_docs=True measurably improves top-3 placement** (+5.1 pp over False) and mean_rank (2.67 vs 2.72). The additional document context sharpens embedding similarity enough to push more students within top-3 even when rank-1 is unavailable.
- **group_bonus adds a score delta (gap≈0.008) but does not improve assignment rank** — the boost is applied but too uniformly to shift students into a higher-ranked slot. This confirms group_boost is score-visible but assignment-neutral.
- **11.4% of students (pooled) are assigned beyond rank 5** — this hard tail is driven by capacity saturation: when the top 5 supervisors are all full, the allocator must use rank 6+. This is an inherent property of the slot constraint, not a model quality issue.
- **cap_priority is score-neutral** — it controls which supervisor's slots fill first but leaves the final score distribution essentially unchanged (Δ < 0.005 on all metrics).
