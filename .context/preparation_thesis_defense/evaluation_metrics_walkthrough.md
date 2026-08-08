# Evaluation Metrics — Step-by-Step Walkthrough

**Date:** 2026-07-29  
Penjelasan perhitungan setiap metrik evaluasi dengan data dummy konsisten dari awal sampai akhir.

---

## Setup: Data Dummy

**5 mahasiswa, 5 supervisor** (disederhanakan dari 168 × 14):

| Mahasiswa | Ground Truth (GT) |
|---|---|
| M1 | D001 |
| M2 | D003 |
| M3 | D002 |
| M4 | D001 |
| M5 | D005 |

**Score matrix** hasil cosine similarity (baris = mahasiswa, kolom = supervisor):

```
         D001   D002   D003   D004   D005
M1:    [ 0.85,  0.72,  0.60,  0.41,  0.30 ]
M2:    [ 0.55,  0.48,  0.79,  0.62,  0.51 ]
M3:    [ 0.40,  0.88,  0.71,  0.55,  0.33 ]
M4:    [ 0.70,  0.65,  0.80,  0.45,  0.38 ]
M5:    [ 0.61,  0.53,  0.44,  0.39,  0.77 ]
```

---

## Step 1 — Tentukan Rank per Mahasiswa

Untuk setiap mahasiswa, urutkan supervisor dari skor tertinggi → cari posisi GT.

**M1** (GT = D001):
```
Urutan skor: D001(0.85) > D002(0.72) > D003(0.60) > D004(0.41) > D005(0.30)
Rank D001 = 1  ✅
```

**M2** (GT = D003):
```
Urutan skor: D003(0.79) > D004(0.62) > D001(0.55) > D005(0.51) > D002(0.48)
Rank D003 = 1  ✅
```

**M3** (GT = D002):
```
Urutan skor: D002(0.88) > D003(0.71) > D004(0.55) > D001(0.40) > D005(0.33)
Rank D002 = 1  ✅
```

**M4** (GT = D001):
```
Urutan skor: D003(0.80) > D001(0.70) > D002(0.65) > D004(0.45) > D005(0.38)
Rank D001 = 2  ⚠️  (kalah dari D003)
```

**M5** (GT = D005):
```
Urutan skor: D005(0.77) > D001(0.61) > D002(0.53) > D003(0.44) > D004(0.39)
Rank D005 = 1  ✅
```

**Tabel rank hasil:**

| Mahasiswa | GT | Rank GT |
|---|---|---|
| M1 | D001 | **1** |
| M2 | D003 | **1** |
| M3 | D002 | **1** |
| M4 | D001 | **2** |
| M5 | D005 | **1** |

---

## Step 2 — Hitung Tiap Metrik

### MRR (Mean Reciprocal Rank)

Formula: `MRR = rata-rata(1 / rank)`

```
M1: 1/1 = 1.000
M2: 1/1 = 1.000
M3: 1/1 = 1.000
M4: 1/2 = 0.500
M5: 1/1 = 1.000

MRR = (1.000 + 1.000 + 1.000 + 0.500 + 1.000) / 5
    = 4.500 / 5
    = 0.900
```

> **Interpretasi**: Rata-rata supervisor GT ada di posisi ke-1.11 dari 5 kandidat.

---

### Hit@1

Formula: `1 jika rank = 1, else 0`

```
M1: rank=1 → 1
M2: rank=1 → 1
M3: rank=1 → 1
M4: rank=2 → 0  ← miss
M5: rank=1 → 1

Hit@1 = (1+1+1+0+1) / 5 = 4/5 = 0.800 (80%)
```

> **Interpretasi**: 80% mahasiswa, supervisor GT-nya muncul di rekomendasi teratas.

---

### Hit@5

Formula: `1 jika rank ≤ 5, else 0`

```
Semua rank ≤ 5 (max rank = 2, total supervisor = 5)
M1: 1, M2: 1, M3: 1, M4: 1, M5: 1

Hit@5 = 5/5 = 1.000 (100%)
```

> **Interpretasi**: Semua supervisor GT masuk dalam top-5 rekomendasi.

---

### nDCG@5

Formula: `1 / log2(rank + 1)` jika rank ≤ 5, else 0

```
M1: 1/log2(1+1) = 1/log2(2) = 1/1.000 = 1.000
M2: 1/log2(1+1) = 1/1.000   = 1.000
M3: 1/log2(1+1) = 1/1.000   = 1.000
M4: 1/log2(2+1) = 1/log2(3) = 1/1.585 = 0.631  ← penalti rank 2
M5: 1/log2(1+1) = 1/1.000   = 1.000

nDCG@5 = (1.000 + 1.000 + 1.000 + 0.631 + 1.000) / 5
        = 4.631 / 5
        = 0.926
```

> **Interpretasi**: nDCG memberi penalti progressif — rank 2 → 0.631, rank 5 → 0.387.

---

### nDCG@10

Formula identik dengan nDCG@5, threshold diperluas ke rank ≤ 10.

Di contoh ini hasilnya sama = **0.926** karena semua rank ≤ 5.

Perbedaan terasa jika ada mahasiswa dengan rank > 5:
```
Contoh mahasiswa dengan rank = 7:
  nDCG@5  → 0      (rank > 5, tidak dihitung)
  nDCG@10 → 1/log2(7+1) = 1/3.000 = 0.333  (masih dihitung)
```

---

### Avg Rank

Formula: `rata-rata(rank)`

```
Avg Rank = (1 + 1 + 1 + 2 + 1) / 5 = 6/5 = 1.2
```

> **Interpretasi**: Rata-rata supervisor GT ada di posisi 1.2 — sangat dekat ke top.

---

## Step 3 — Assignment Match Rate (Post-Solver)

Dihitung **setelah greedy solver** menentukan penugasan final, bukan dari ranking.

Misal output solver:

| Mahasiswa | GT | Assigned | Match? |
|---|---|---|---|
| M1 | D001 | D001 | ✅ |
| M2 | D003 | D003 | ✅ |
| M3 | D002 | D002 | ✅ |
| M4 | D001 | D003 | ❌ (D001 sudah penuh, solver paksa pindah ke D003) |
| M5 | D005 | D005 | ✅ |

```
Match Rate = 4/5 = 0.800 (80%)
```

> **Kunci**: M4 di ranking merekomendasikan D001 tapi solver tidak bisa karena constraint kapasitas.  
> Inilah mengapa **Match Rate selalu ≤ Hit@1** pada sistem dengan constraint kapasitas.

---

## Ringkasan Hasil Dummy

| Metrik | Nilai Dummy | Baseline Acak (5 sup) |
|---|---|---|
| MRR | **0.900** | 0.457 |
| Hit@1 | **80%** | 20% |
| Hit@5 | **100%** | 100% |
| nDCG@5 | **0.926** | 0.423 |
| Avg Rank | **1.2** | 3.0 |
| Match Rate | **80%** | ~20% |

---

## Nilai Aktual Run 26 (168 mahasiswa, 14 supervisor)

| Metrik | Run 26 (bge-m3) | Baseline Acak (14 sup) |
|---|---|---|
| MRR | **0.711** | 0.232 |
| Hit@1 | **71.4%** | 7.1% |
| Hit@5 | **92.9%** | 35.7% |
| nDCG@5 | — | 0.211 |
| Avg Rank | — | 7.5 |
| Match Rate | **53%** | ~7.1% |

Selisih MRR: **0.711 vs 0.232** → sistem 3× lebih baik dari random baseline.  
Ini argumen kuantitatif utama validitas pendekatan semantic similarity.
