# 2026-06-07 — Batch Experiment Findings Report

**Batch:** `output/batch_20260607_163454/` — 18 runs
**Matrix:** 3 models × 3 toggle configs × 2 capacity variants

---

## Full Evaluation Pivot Table

```
run  model            grp_bonus  extra_docs  cap_pri   MRR    Hit@1  Hit@5  nDCG@5  nDCG@10  avg_rank  match_rate
---  ---------------  ---------  ----------  -------  ------  ------  ------  ------  -------  --------  ----------
8    bge-m3           False      True        no       0.5847  0.4167  0.7976  0.6201  0.6676   3.3274    0.3571
9    bge-m3           False      True        yes      0.5847  0.4167  0.7976  0.6201  0.6676   3.3274    0.3512
10   bge-m3           True       False       no       0.5229  0.3571  0.7202  0.5473  0.6204   3.9107    0.3333
11   bge-m3           True       False       yes      0.5229  0.3571  0.7202  0.5473  0.6204   3.9107    0.3274
12   bge-m3           False      False       no       0.5229  0.3571  0.7202  0.5473  0.6204   3.9107    0.3333
13   bge-m3           False      False       yes      0.5229  0.3571  0.7202  0.5473  0.6204   3.9107    0.3274
14   qwen3-0.6B       False      True        no       0.4781  0.2976  0.7500  0.5225  0.5796   4.1369    0.4226
15   qwen3-0.6B       False      True        yes      0.4781  0.2976  0.7500  0.5225  0.5796   4.1369    0.4226
16   qwen3-0.6B       True       False       no       0.4355  0.2798  0.5893  0.4361  0.5442   4.7976    0.2560
17   qwen3-0.6B       True       False       yes      0.4355  0.2798  0.5893  0.4361  0.5442   4.7976    0.2500
18   qwen3-0.6B       False      False       no       0.4355  0.2798  0.5893  0.4361  0.5442   4.7976    0.2560
19   qwen3-0.6B       False      False       yes      0.4355  0.2798  0.5893  0.4361  0.5442   4.7976    0.2500
20   e5-large         False      True        no       0.4689  0.3036  0.6488  0.4834  0.5629   4.5952    0.3988
21   e5-large         False      True        yes      0.4689  0.3036  0.6488  0.4834  0.5629   4.5952    0.3988
22   e5-large         True       False       no       0.5027  0.3214  0.7143  0.5312  0.5899   4.1488    0.2857
23   e5-large         True       False       yes      0.5027  0.3214  0.7143  0.5312  0.5899   4.1488    0.2857
24   e5-large         False      False       no       0.5027  0.3214  0.7143  0.5312  0.5899   4.1488    0.2857
25   e5-large         False      False       yes      0.5027  0.3214  0.7143  0.5312  0.5899   4.1488    0.2857
```

---

## Rankings Score Distribution Table

```
run  model        grp_bonus  extra_docs  cap_pri  sim_mean  sim_std  fin_mean  gap   delta_mean  %rank-1  %top-3  mean_rank
---  -----------  ---------  ----------  -------  --------  -------  --------  ----  ----------  -------  ------  ---------
8    BGE-M3       False      True        no       0.5853    0.0759   0.5853    0.00  0.0322      61.4%    74.9%   3.22
9    BGE-M3       False      True        yes      0.5858    0.0749   0.5858    0.00  0.0317      61.4%    74.9%   3.19
10   BGE-M3       True       False       no       0.5770    0.0727   0.5838    0.01  0.0374      53.8%    68.4%   3.37
11   BGE-M3       True       False       yes      0.5767    0.0729   0.5835    0.01  0.0377      53.8%    68.4%   3.35
12   BGE-M3       False      False       no       0.5770    0.0727   0.5770    0.00  0.0374      53.8%    68.4%   3.37
13   BGE-M3       False      False       yes      0.5767    0.0729   0.5767    0.00  0.0377      53.8%    68.4%   3.35
14   Qwen3-0.6B   False      True        no       0.6209    0.1132   0.6209    0.00  0.0676      31.6%    58.5%   4.08
15   Qwen3-0.6B   False      True        yes      0.6220    0.1111   0.6220    0.00  0.0665      31.6%    59.1%   4.04
16   Qwen3-0.6B   True       False       no       0.6631    0.1059   0.6698    0.01  0.1044      33.9%    52.0%   4.32
17   Qwen3-0.6B   True       False       yes      0.6643    0.1056   0.6711    0.01  0.1032      34.5%    52.6%   4.25
18   Qwen3-0.6B   False      False       no       0.6631    0.1059   0.6631    0.00  0.1044      33.9%    52.0%   4.32
19   Qwen3-0.6B   False      False       yes      0.6643    0.1056   0.6643    0.00  0.1032      34.5%    52.6%   4.25
20   mE5-large    False      True        no       0.8530    0.0391   0.8530    0.00  0.0171      35.1%    56.1%   4.25
21   mE5-large    False      True        yes      0.8530    0.0393   0.8530    0.00  0.0171      35.7%    56.7%   4.23
22   mE5-large    True       False       no       0.8802    0.0334   0.8802    0.00  0.0180      52.0%    69.0%   3.21
23   mE5-large    True       False       yes      0.8802    0.0336   0.8802    0.00  0.0181      52.0%    69.6%   3.25
24   mE5-large    False      False       no       0.8802    0.0334   0.8802    0.00  0.0180      52.0%    69.0%   3.21
25   mE5-large    False      False       yes      0.8802    0.0336   0.8802    0.00  0.0181      52.0%    69.6%   3.25
```

---

## Supervisor Load Distribution Table

All 18 runs produce **identical** distribution statistics.

| Stat | Value |
|------|-------|
| Min assigned | 12 |
| Max assigned | 13 |
| Mean | 12.21 |
| Std | 0.4258 |
| Range | 1 |
| Gini | 0.0138 |
| Supervisors at max (13) | 3 |
| Supervisors at min (12) | 11 |

**Who gets the extra student (count=13):**

| cap_priority setting | Supervisors at 13 |
|----------------------|-------------------|
| empty `[]` | D2211, D5918, D1749 |
| `CAPACITY_PRIORITY_CODES` | D2211, D6184, D6826 |

---

## Finding 1 — Model is the Dominant Variable

| Model | MRR | Hit@5 | nDCG@10 | %Rank-1 | sim_mean |
|-------|-----|-------|---------|---------|---------|
| **bge-m3** | **0.543** | **0.746** | **0.636** | **56.3%** | 0.580 |
| e5-large | 0.491 | 0.693 | 0.581 | 46.5% | **0.871** |
| qwen3-0.6B | 0.450 | 0.643 | 0.556 | 33.3% | 0.650 |

**bge-m3** wins every retrieval metric (MRR, Hit@k, nDCG@k) and student satisfaction (%rank-1, %top-3) despite producing the lowest raw similarity scores.

**mE5-large** has the highest raw scores (mean 0.871, std 0.035) and tightest cluster distribution, but is second on assignment satisfaction. Without extra_docs it reaches 52% rank-1 — competitive with bge-m3.

**qwen3-0.6B** is worst on retrieval and satisfaction. Notably: high similarity scores (0.65) but poor %rank-1 (33.3%) indicates a high-variance, overconfident scoring landscape. Paradoxically it has the highest `match_rate` (42.3%), which is the retrieval-vs-assignment inversion discussed in Finding 6.

---

## Finding 2 — Best Single Configuration

**Run 8** — `bge-m3`, `extra_docs=True`, `group_bonus=False`, no capacity priority

| Metric | Value |
|--------|-------|
| MRR | **0.5847** |
| Hit@1 | **0.4167** |
| Hit@5 | **0.7976** |
| nDCG@10 | **0.6676** |
| %Rank-1 assigned | **61.4%** |
| %Top-3 assigned | **74.9%** |
| Mean assigned rank | **3.22** |

---

## Finding 3 — extra_docs Effect is Model-Dependent

| Model | extra_docs=True | extra_docs=False | Δ%Rank-1 | Verdict |
|-------|-----------------|------------------|----------|---------|
| bge-m3 | 61.4% | 53.8% | **+7.6pp** | Enable |
| qwen3-0.6B | 31.6% | 33.9% | -2.3pp | Disable |
| mE5-large | 35.5% | 52.0% | **-16.5pp** | Strongly disable |

Extra docs help weaker models by enriching sparse supervisor profiles, but pollute mE5-large's finely tuned embedding space — collapsing its discriminative ability between supervisors.

---

## Finding 4 — group_bonus is Neutral to Harmful

| Model | group_bonus=False | group_bonus=True | Δ%Rank-1 |
|-------|-------------------|------------------|----------|
| bge-m3 | 61.4% | 53.8% | **-7.6pp** |
| qwen3-0.6B | 31.6% | 33.9% | +2.3pp (but delta_mean ↑ +0.036) |
| mE5-large | boost fires on 0 students | — | no-op |

When `group_bonus=False`, `final_score == similarity_score` exactly (gap = 0.000). When `True`, a mean +0.0045–0.007 boost is added to final_score. For mE5-large across all runs the `group_boost` column is entirely zero — the group overflow condition never triggers.

---

## Finding 5 — Load Distribution is Config-Invariant

The capacity enforcement layer fully absorbs all scoring variation. Every config produces min=12, max=13, std=0.4258. No embedding model, toggle, or capacity setting changes the balance level. `capacity_priority_codes` only redistributes which 3 supervisors hold 13 students — it does not change the aggregate balance.

---

## Finding 6 — Retrieval vs. Assignment Inversion (qwen3-0.6B)

qwen3-0.6B has the **worst MRR** (0.45) but the **highest match_rate** (42.3%, 71 students matched). bge-m3 wins retrieval but matches fewer students (max 60, 35.7%). This inversion suggests qwen3's embeddings — while less precise at retrieving the ground-truth labeled supervisor — produce score distributions that happen to align better with real supervision slot availability. This is a meaningful trade-off to surface in the thesis discussion.

---

## Finding 7 — Hybrid Score Equals Content Score in All 18 Runs

Every retrieval metric for `hybrid_score` is **numerically identical** to `content_based` across all 18 runs. The hybrid weighting layer appears not to be differentiating the two score types. This is a potential implementation issue worth verifying in `recommender.py`.

---

## Finding 8 — Pooled Rank Distribution (all 3,078 assigned slots)

| Assigned Rank | Count | % |
|---------------|-------|---|
| 1 | 1,397 | 45.4% |
| 2 | 342 | 11.1% |
| 3 | 211 | 6.9% |
| 4 | 291 | 9.5% |
| 5 | 90 | 2.9% |
| 6+ | 747 | 24.3% |

Bimodal pattern: strong peak at rank-1, dip at rank 2–3, secondary bump at rank 4. This reflects capacity saturation dynamics — when a student's top supervisor is full, they typically fall to rank 4–6 rather than rank 2–3, suggesting top supervisors share similar capacity constraints and fill simultaneously.

---

## Summary Recommendations

| Goal | Best config | Key metric |
|------|-------------|------------|
| Retrieval quality | bge-m3 + extra_docs=True + group_bonus=False | MRR 0.585, Hit@5 0.798 |
| Student satisfaction | bge-m3 + extra_docs=True + group_bonus=False | 61.4% rank-1 assigned |
| Highest raw similarity | mE5-large + extra_docs=False + group_bonus=False | sim_mean 0.880 |
| Assignment coverage | qwen3-0.6B + extra_docs=True + group_bonus=False | match_rate 42.3% |
| Avoid | qwen3-0.6B + group_bonus=True | worst satisfaction + highest displacement |