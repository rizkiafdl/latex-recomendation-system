# Cosine Similarity — Penjelasan Simple

**Date:** 2026-07-29

---

## 1. Alur Keseluruhan

```
Teks mahasiswa + dosen
      ↓ model.encode()
Vektor mentah  (panjang bebas, dimensi bisa negatif)
      ↓ L2 normalize (normalize_embeddings=True)
Unit vector    (panjang = 1, dimensi masih bisa negatif)
      ↓ np.matmul(student_vectors, supervisor_vectors.T)
Score matrix   (range -1 sampai +1) = cosine similarity
```

---

## 2. L2 Normalization

### Apa itu?

L2 normalize = memaksa **panjang vektor menjadi 1** (unit vector).  
Bukan membatasi nilai ke 0–1. Nilai tiap dimensi masih bisa negatif.

### Formula panjang vektor

```
||v|| = √(v1² + v2² + v3² + ... + vn²)
```

### Contoh

```
Vektor asli:      [3.0, -1.5, 4.2, -0.8]
Panjang = √(3² + 1.5² + 4.2² + 0.8²) = √(9 + 2.25 + 17.64 + 0.64) = √29.53 ≈ 5.43

Setelah normalize: [3/5.43, -1.5/5.43, 4.2/5.43, -0.8/5.43]
                 = [0.55,   -0.28,      0.77,     -0.15]

Panjang baru = √(0.55² + 0.28² + 0.77² + 0.15²) = 1.0  ✅
```

### Mengapa perlu normalize?

Tanpa normalisasi, teks panjang menghasilkan vektor dengan magnitude besar → dot product lebih besar bukan karena lebih relevan, tapi karena lebih panjang. Normalisasi menghilangkan **bias panjang dokumen** — yang diukur murni **arah (makna)**, bukan kuantitas teks.

---

## 3. Cosine Similarity

### Formula umum

```
cosine(A, B) = (A · B) / (||A|| × ||B||)
```

### Setelah L2 normalize (||A|| = ||B|| = 1)

```
cosine(A, B) = (A · B) / (1 × 1) = A · B
```

Dot product langsung = cosine similarity. Tidak perlu pembagian lagi.

### Range nilai

```
+1  → arah persis sama   (makna identik)
 0  → tegak lurus        (tidak ada hubungan)
-1  → arah berlawanan    (makna bertolak belakang)
```

### Contoh dot product 4-dimensi

```
A = [0.6,  0.1,  0.7,  0.2]   (unit vector, panjang = 1)
B = [0.7,  0.2,  0.6,  0.1]   (unit vector, panjang = 1)
C = [-0.1, 0.8,  0.3,  0.5]   (unit vector, panjang = 1)

A · B = (0.6×0.7) + (0.1×0.2) + (0.7×0.6) + (0.2×0.1)
      = 0.42 + 0.02 + 0.42 + 0.02 = 0.88   ← sangat mirip

A · C = (0.6×-0.1) + (0.1×0.8) + (0.7×0.3) + (0.2×0.5)
      = -0.06 + 0.08 + 0.21 + 0.10 = 0.33  ← kurang mirip
```

---

## 4. Kenapa Hasil Praktis 0–1 (Bukan -1 sampai +1)?

Secara matematis range cosine similarity adalah **-1 sampai +1**.

Nilai negatif muncul jika dua vektor berlawanan arah — artinya makna bertolak belakang total.

Dalam embedding teks natural (profil mahasiswa vs profil dosen), vektor cenderung berada di **kuadran yang sama** di ruang berdimensi tinggi — tidak pernah bermakna "berlawanan total". Hasilnya praktis 0–1, tapi ini **karakteristik data**, bukan batasan matematis.

---

## 5. Matmul = Semua Pasangan Sekaligus

```python
similarity = np.matmul(student_vectors, supervisor_vectors.T)
# (168, 1024) × (1024, 14) → (168, 14)
```

Setiap sel `[i, j]` = cosine similarity mahasiswa-i vs supervisor-j.

```
             D001   D002   D003  ...  D014
mahasiswa 0: [0.85,  0.72,  0.60, ..., 0.30]
mahasiswa 1: [0.55,  0.48,  0.79, ..., 0.51]
...
mahasiswa 167: [...]
```

168 × 14 = **2.352 pasangan** dihitung dalam satu operasi matrix.

---

## 6. Perbedaan L2 Normalize vs Min-Max Normalize

| | L2 Normalize | Min-Max Normalize |
|---|---|---|
| Yang dibatasi | Panjang vektor = 1 | Nilai output ke range [0, 1] |
| Nilai dimensi | Bisa negatif | Selalu 0–1 |
| Dipakai untuk | Cosine similarity | Feature scaling |
| Di thesis ini | ✅ Dipakai | ❌ Tidak dipakai |