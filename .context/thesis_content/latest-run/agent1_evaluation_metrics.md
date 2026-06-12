# Agent 1 — Evaluation Metrics Analysis

**Runs analyzed:** 26–43 (18 files)  
**Date:** 2026-06-09  
**Method:** IR-style offline evaluation; ground truth = `current_supervisor_code`; 168 students evaluated per run.

---

## 1. Full Run Comparison Table

| Run | Model | group_bonus | extra_docs | cap_priority | MRR | Hit@1 | Hit@5 | nDCG@5 | nDCG@10 | Avg Rank | Match Rate | Matched |
|-----|-------|-------------|------------|--------------|-----|-------|-------|--------|---------|----------|------------|---------|
| 26 | bge-m3 | False | True | — | **0.7111** | **0.5595** | **0.9286** | **0.7585** | **0.7785** | **2.17** | 0.5298 | 89 |
| 27 | bge-m3 | False | True | ✓ | 0.7111 | 0.5595 | 0.9286 | 0.7585 | 0.7785 | 2.17 | 0.5357 | 90 |
| 28 | bge-m3 | True | False | — | 0.6346 | 0.4881 | 0.8274 | 0.6673 | 0.7078 | 3.04 | 0.4940 | 83 |
| 29 | bge-m3 | True | False | ✓ | 0.6346 | 0.4881 | 0.8274 | 0.6673 | 0.7078 | 3.04 | 0.4881 | 82 |
| 30 | bge-m3 | False | False | — | 0.6346 | 0.4881 | 0.8274 | 0.6673 | 0.7078 | 3.04 | 0.4940 | 83 |
| 31 | bge-m3 | False | False | ✓ | 0.6346 | 0.4881 | 0.8274 | 0.6673 | 0.7078 | 3.04 | 0.4881 | 82 |
| 32 | qwen3-0.6b | False | True | — | 0.6168 | 0.4702 | 0.8512 | 0.6597 | 0.6979 | 2.95 | 0.5119 | 86 |
| 33 | qwen3-0.6b | False | True | ✓ | 0.6168 | 0.4702 | 0.8512 | 0.6597 | 0.6979 | 2.95 | 0.5238 | 88 |
| 34 | qwen3-0.6b | True | False | — | 0.5552 | 0.4226 | 0.7560 | 0.5789 | 0.6488 | 3.52 | 0.4226 | 71 |
| 35 | qwen3-0.6b | True | False | ✓ | 0.5552 | 0.4226 | 0.7560 | 0.5789 | 0.6488 | 3.52 | 0.4286 | 72 |
| 36 | qwen3-0.6b | False | False | — | 0.5552 | 0.4226 | 0.7560 | 0.5789 | 0.6488 | 3.52 | 0.4226 | 71 |
| 37 | qwen3-0.6b | False | False | ✓ | 0.5552 | 0.4226 | 0.7560 | 0.5789 | 0.6488 | 3.52 | 0.4286 | 72 |
| 38 | me5-large | False | True | — | 0.6716 | 0.5060 | 0.9048 | 0.7205 | 0.7479 | 2.43 | **0.5833** | **98** |
| 39 | me5-large | False | True | ✓ | 0.6716 | 0.5060 | 0.9048 | 0.7205 | 0.7479 | 2.43 | 0.5714 | 96 |
| 40 | me5-large | True | False | — | 0.6013 | 0.4345 | 0.8274 | 0.6421 | 0.6875 | 3.12 | 0.4762 | 80 |
| 41 | me5-large | True | False | ✓ | 0.6013 | 0.4345 | 0.8274 | 0.6421 | 0.6875 | 3.12 | 0.4821 | 81 |
| 42 | me5-large | False | False | — | 0.6013 | 0.4345 | 0.8274 | 0.6421 | 0.6875 | 3.12 | 0.4762 | 80 |
| 43 | me5-large | False | False | ✓ | 0.6013 | 0.4345 | 0.8274 | 0.6421 | 0.6875 | 3.12 | 0.4821 | 81 |

> **Note:** content_based and hybrid_score metrics are always identical in this batch — group_boost is too small to change ranking order.

---

## 2. Group-by Aggregations

### By Embedding Model

| Model | MRR | Hit@1 | Hit@5 | nDCG@5 | nDCG@10 | Avg Rank | Match Rate |
|-------|-----|-------|-------|--------|---------|----------|------------|
| **bge-m3** | **0.6601** | **0.5119** | **0.8611** | **0.6977** | **0.7314** | **2.748** | 0.5050 |
| me5-large | 0.6247 | 0.4583 | 0.8532 | 0.6682 | 0.7076 | 2.889 | **0.5119** |
| qwen3-0.6b | 0.5758 | 0.4385 | 0.7877 | 0.6058 | 0.6652 | 3.331 | 0.4563 |

### By `extra_docs`

| extra_docs | MRR | Hit@1 | Hit@5 | nDCG@5 | nDCG@10 | Avg Rank | Match Rate |
|-----------|-----|-------|-------|--------|---------|----------|------------|
| **True** | **0.6665** | **0.5119** | **0.8948** | **0.7129** | **0.7414** | **2.516** | **0.5427** |
| False | 0.5970 | 0.4484 | 0.8036 | 0.6294 | 0.6814 | 3.226 | 0.4653 |

### By `group_bonus`

| group_bonus | MRR | Hit@1 | Hit@5 | nDCG@5 | nDCG@10 | Avg Rank | Match Rate |
|------------|-----|-------|-------|--------|---------|----------|------------|
| False | 0.6318 | 0.4802 | 0.8492 | 0.6712 | 0.7114 | 2.871 | 0.5040 |
| True | 0.5970 | 0.4484 | 0.8036 | 0.6294 | 0.6814 | 3.226 | 0.4653 |

> **Caution:** In this batch, `group_bonus=True` always coincides with `extra_docs=False` — the two variables are **fully confounded**. The apparent disadvantage of group_bonus is attributable to extra_docs=False, not the bonus itself.

### By `cap_priority`

| cap_priority | MRR | Hit@1 | Hit@5 | nDCG@5 | Avg Rank | Match Rate |
|-------------|-----|-------|-------|--------|----------|------------|
| False | 0.6202 | 0.4696 | 0.8340 | 0.6572 | 2.989 | 0.4901 |
| True | 0.6202 | 0.4696 | 0.8340 | 0.6572 | 2.989 | 0.4921 |

> **Finding:** `capacity_priority_codes` has **zero effect on retrieval metrics** (MRR, Hit, nDCG identical). It produces a marginal +0.002 difference in match_rate, which is within noise.

---

## 3. Best and Worst Run per Metric

| Metric | Best Run | Best Value | Worst Run | Worst Value |
|--------|----------|------------|-----------|-------------|
| MRR | Run 26 (bge-m3, extra_docs=True) | **0.7111** | Run 34 (qwen3, group_bonus=True) | 0.5552 |
| Hit@1 | Run 26 | **0.5595** | Run 34 | 0.4226 |
| Hit@5 | Run 26 | **0.9286** | Run 34 | 0.7560 |
| nDCG@5 | Run 26 | **0.7585** | Run 34 | 0.5789 |
| nDCG@10 | Run 26 | **0.7785** | Run 34 | 0.6488 |
| Avg Rank | Run 26 | **2.17** | Run 34 | 3.52 |
| Match Rate | Run 38 (me5-large, extra_docs=True) | **0.5833** | Run 34 | 0.4226 |

---

## 4. Key Insights

- **bge-m3 is the best retrieval model.** It achieves the highest MRR (0.660), Hit@1 (0.512), and nDCG@5 (0.698) on average — despite having the lowest absolute similarity scores, its scores are more discriminative.
- **`extra_docs=True` is the single most impactful config.** Enabling extra documents raises MRR from 0.597 → 0.667 (+0.070), Hit@1 from 0.448 → 0.512 (+0.064), and avg_rank from 3.23 → 2.52. This is the clearest lever in this batch.
- **`capacity_priority_codes` is evaluation-neutral.** All retrieval metrics are exactly identical with and without it. It only affects slot-level assignment decisions.
- **group_bonus and extra_docs are confounded** — they were never varied independently in this batch (group_bonus=True → extra_docs always False). The apparent metric gap attributed to group_bonus is actually the extra_docs effect.
- **me5-large achieves the highest match_rate (0.583, run 38)** despite ranking 2nd on MRR — suggesting its ranking aligns better with how the actual assignment algorithm distributes students.
- **The worst single run is run 34** (qwen3-0.6b + group_bonus=True + extra_docs=False): MRR=0.555, Hit@1=0.423, avg_rank=3.52 — the combined effect of the weakest model and no extra docs.
- **Hybrid scores = content-based scores** across all runs: the group_boost contribution is too small to change retrieval order, making the hybrid evaluation redundant here.
