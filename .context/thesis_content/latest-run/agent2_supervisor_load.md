# Agent 2 — Supervisor Load Distribution Analysis

**Runs analyzed:** 26–43 (18 files)  
**Date:** 2026-06-09  
**Supervisors:** 14 total | **Capacity range:** 10–13 (target min=10, target max=12/13)

---

## 1. Per-Run Load Distribution Stats

| Run | Model | group_bonus | extra_docs | cap_priority | Min | Max | Mean | Std | Range | n@max | Gini |
|-----|-------|-------------|------------|--------------|-----|-----|------|-----|-------|-------|------|
| 26 | bge-m3 | False | True | — | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 27 | bge-m3 | False | True | ✓ | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 28 | bge-m3 | True | False | — | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 29 | bge-m3 | True | False | ✓ | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 30 | bge-m3 | False | False | — | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 31 | bge-m3 | False | False | ✓ | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 32 | qwen3-0.6b | False | True | — | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 33 | qwen3-0.6b | False | True | ✓ | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 34 | qwen3-0.6b | True | False | — | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 35 | qwen3-0.6b | True | False | ✓ | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 36 | qwen3-0.6b | False | False | — | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 37 | qwen3-0.6b | False | False | ✓ | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 38 | me5-large | False | True | — | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 39 | me5-large | False | True | ✓ | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 40 | me5-large | True | False | — | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 41 | me5-large | True | False | ✓ | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 42 | me5-large | False | False | — | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |
| 43 | me5-large | False | False | ✓ | 12 | 13 | 12.21 | 0.41 | 1 | 3 | 0.01378 |

---

## 2. Key Structural Finding: Load Distribution is Config-Invariant

**All 18 runs produce an identical load distribution:**

| Metric | Value |
|--------|-------|
| Min assigned | 12 |
| Max assigned | 13 |
| Mean assigned | 12.21 |
| Std | 0.41 |
| Range (max−min) | 1 |
| Supervisors at max (13) | 3 of 14 |
| Gini coefficient | **0.01378** (near-zero inequality) |

The Gini coefficient of 0.014 indicates an essentially uniform load. The slot allocator distributes students within a fixed ±1 band regardless of embedding model, group_bonus, extra_docs, or capacity_priority_codes.

**Why:** 170 students ÷ 14 supervisors = 12.14 → the allocator must assign 12 to 11 supervisors and 13 to 3 supervisors (3×13 + 11×12 = 171). The choice of *which* 3 supervisors get 13 is governed by `capacity_priority_codes`, but the *aggregate distribution statistics* are mathematically fixed by the student-to-supervisor ratio.

---

## 3. Supervisors at Maximum Capacity — Frequency

All 14 supervisors appear at their maximum capacity in all 18 runs (100% frequency). This is because every supervisor is assigned exactly at their individual cap ceiling (12 or 13). The system fully saturates all slots.

### Supervisors with Extended Capacity (13 students, i.e. max_capacity=13)

Consistent across all 18 runs:

| Supervisor Code | Name |
|-----------------|------|
| D2211 | Dr. Abdul Haris Rangkuti, S.Kom., M.M., M.Si. |
| D5918 | Dr. Boby Siswanto, S.T., M.T. |
| D1749 | Dr. Johan Muliadi Kerta, S.Kom., M.M. |

These 3 supervisors receive the overflow slot in every run.

> **Note:** `capacity_priority_codes` includes D2211 and D1749 — both are already in the extended-capacity group regardless of the flag. D6184 and D6826 (also in the priority list) remain at 12 students. This suggests the priority codes influence tie-breaking within the slot allocator, not the capacity ceiling itself.

---

## 4. Effect of Config Dimensions on Load Distribution

| Dimension | Effect on load balance |
|-----------|----------------------|
| `embedding_model` | None — Gini = 0.01378 across all 3 models |
| `group_bonus` | None — identical distribution with and without |
| `extra_docs` | None — identical distribution |
| `cap_priority` | None on aggregate stats; may shift which specific students are assigned to priority supervisors, but all-supervisor distribution unchanged |

---

## 5. Key Insights

- **Load distribution is perfectly stable.** No configuration parameter changes the aggregate assignment balance. The slot allocator is deterministic at the aggregate level — this is a strong result for fairness: all supervisors are within 1 student of each other regardless of model or config.
- **Gini = 0.014 is near-optimal.** For a system with integer capacity constraints, this is the minimum achievable inequality. The allocator achieves maximum possible fairness.
- **Three supervisors consistently bear the extra slot** (D2211, D5918, D1749). These are presumably the supervisors with the highest acceptance capacity or highest priority. If load equity across these three vs. the rest is a concern, the `capacity_priority_codes` mechanism is the correct lever, but it needs to include D5918 to fully control the overflow set.
- **`capacity_priority_codes` does not equalize** D6184 and D6826's load relative to D5918 — those two priority-list supervisors remain at 12. The overflow always flows to D2211, D5918, D1749 regardless of cap_priority setting.
- **Total students assigned:** 171 per run (14 supervisors × 12.21 mean), constant across all 18 runs.
