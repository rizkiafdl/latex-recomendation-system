# Greedy Capacity Solver — Penjelasan Mendalam

**Date:** 2026-07-29

Penjelasan dua komponen utama: Capacity Planning (batas kuota) dan Greedy Solver (algoritma penugasan).

---

## Konteks Aktual Thesis

```
168 mahasiswa valid, 14 supervisor
target min = 10, max = 12 per supervisor
total max capacity = 14 × 12 = 168  → pas, tidak overflow
total min capacity = 14 × 10 = 140  → tidak underflow
```

Contoh di bawah memakai **6 mahasiswa, 3 supervisor** agar mudah ditelusuri.

---

## Bagian 1 — Capacity Planning

### Step 1: Inisialisasi Batas

```
min_caps = [2, 2, 2]   # setiap supervisor minimal 2 mahasiswa
max_caps = [2, 2, 2]   # setiap supervisor maksimal 2 mahasiswa
```

### Step 2: Ranking Supervisor Berdasarkan Prioritas

```
priority_codes = ["D001"]

ranked = [D001, D002, D003]
# D001 di depan karena prioritas, sisanya urut index
```

### Step 3: Cek Overflow (mahasiswa > total max)

```
Contoh: 10 mahasiswa, total max = 9 → overflow = 1

Distribusi ke priority supervisor (round-robin):
  D001: max_cap → 3   (dapat overflow slot)
  D002: max_cap → 2   (tetap)
  D003: max_cap → 2   (tetap)

Non-priority supervisor tetap hard-capped.
```

### Step 4: Cek Underflow (mahasiswa < total min)

```
Contoh: 4 mahasiswa, total min = 6 → deficit = 2

Kurangi min_cap dari supervisor prioritas TERENDAH (urutan terbalik):
  D003: min_cap → 1   (dikurangi 1)
  D002: min_cap → 1   (dikurangi 1)
  D001: min_cap → 2   (tidak disentuh, prioritas tertinggi)
```

### Kasus Aktual Thesis

```
overflow  = max(0, 168 - 168) = 0   → tidak ada
underflow = max(0, 140 - 168) = 0   → tidak ada

min_caps = [10, 10, ..., 10]   (14 supervisor)
max_caps = [12, 12, ..., 12]   (14 supervisor)
```

---

## Bagian 2 — Greedy Solver

Input: score matrix hasil cosine similarity + min_caps + max_caps dari Bagian 1.

### Score Matrix Contoh

```
         D001   D002   D003
M0:    [ 0.90,  0.60,  0.40 ]
M1:    [ 0.85,  0.55,  0.35 ]
M2:    [ 0.82,  0.88,  0.50 ]
M3:    [ 0.80,  0.80,  0.45 ]
M4:    [ 0.40,  0.45,  0.92 ]
M5:    [ 0.35,  0.42,  0.88 ]

min_caps = [2, 2, 2]
max_caps = [2, 2, 2]
```

---

### Phase 1 — Argmax: Setiap Mahasiswa ke Supervisor Terbaik

```python
assignment = np.argmax(score_matrix, axis=1)
```

Ambil nilai tertinggi per baris:

```
M0: max(0.90, 0.60, 0.40) → D001  ✅
M1: max(0.85, 0.55, 0.35) → D001  ✅
M2: max(0.82, 0.88, 0.50) → D002  ✅  (0.88 tertinggi, bukan D001)
M3: max(0.80, 0.80, 0.45) → D001  ✅  (tie D001=D002, ambil index pertama)
M4: max(0.40, 0.45, 0.92) → D003  ✅
M5: max(0.35, 0.42, 0.88) → D003  ✅

assignment = [D001, D001, D002, D001, D003, D003]
counts     = [  3,    1,    2 ]
```

State setelah Phase 1:

```
D001: M0, M1, M3  → count=3, max=2 → OVERFULL ⚠️
D002: M2          → count=1, min=2 → UNDERFULL ⚠️
D003: M4, M5      → count=2        → OK ✅
```

---

### Phase 2 — Overflow Fix Loop

D001 kelebihan 1 mahasiswa. Cari mahasiswa yang **paling tidak rugi** dipindah ke D002.

**Formula penalti:**
```
penalti = skor_di_supervisor_sekarang - skor_di_supervisor_tujuan
```

Penalti kecil = mahasiswa tidak banyak kehilangan kualitas jika dipindah.  
Penalti = 0 = mahasiswa benar-benar tidak rugi dipindah.

```
Kandidat dari D001 → tujuan D002:

M0: 0.90 - 0.60 = 0.30   (rugi 0.30 jika pindah)
M1: 0.85 - 0.55 = 0.30   (rugi 0.30 jika pindah)
M3: 0.80 - 0.80 = 0.00   ← TERENDAH, tidak rugi sama sekali

→ Pilih M3, pindah dari D001 ke D002
```

State setelah Phase 2:

```
assignment = [D001, D001, D002, D002, D003, D003]
counts     = [  2,    2,    2 ]   ← semua dalam batas ✅
```

---

### Phase 3 — Underflow Fix Loop

Tidak diperlukan di contoh ini — semua supervisor sudah memenuhi min_cap setelah Phase 2.

Jika ada underfull, logikanya sama: ambil mahasiswa dari supervisor yang masih kelebihan, pilih yang penaltinya paling kecil.

---

### Final Validation

```python
for j in range(3):
    assert min_caps[j] <= counts[j] <= max_caps[j]
# semua terpenuhi ✅

objective = sum(score_matrix[i, assignment[i]] for i in range(6))
# = 0.90 + 0.85 + 0.88 + 0.80 + 0.92 + 0.88 = 5.23
```

`objective` = total skor penugasan akhir yang berhasil dipertahankan.

---

## Hasil Final

| Mahasiswa | Supervisor Akhir | Skor | Catatan |
|---|---|---|---|
| M0 | D001 | 0.90 | Pilihan pertama, langsung |
| M1 | D001 | 0.85 | Pilihan pertama, langsung |
| M2 | D002 | 0.88 | Pilihan pertama (argmax), langsung |
| M3 | D002 | 0.80 | Dipindah dari D001, penalti = 0.00 |
| M4 | D003 | 0.92 | Pilihan pertama, langsung |
| M5 | D003 | 0.88 | Pilihan pertama, langsung |

---

## Ringkasan Logika

```
Phase 1: Semua mahasiswa → supervisor favorit (argmax)
         Hasilnya: mungkin ada yang overfull / underfull

Phase 2: Kurangi yang kelebihan
         → pindahkan mahasiswa dengan penalti terkecil
         → penalti = selisih skor sekarang vs tujuan

Phase 3: Penuhi yang kekurangan
         → ambil dari supervisor yang masih kelebihan
         → lagi-lagi pilih penalti terkecil

Tujuan: memaksimalkan total skor penugasan
        sambil memenuhi constraint min/max per supervisor
```

---

## Mengapa Disebut "Greedy"?

Setiap iterasi hanya memilih **satu langkah terbaik saat itu** (penalti terkecil), tanpa melihat dampak ke iterasi berikutnya. Ini berbeda dari optimal global (brute-force semua kombinasi).

Trade-off:
- Brute-force: hasil optimal, tapi O(N!) — tidak mungkin untuk 168 mahasiswa
- Greedy: hasil mendekati optimal, O(N × M × iterasi) — cepat dan praktis

Pada data aktual thesis (168 × 14), solver selalu menemukan solusi valid tanpa RuntimeError — artinya constraint kapasitas selalu terpenuhi.