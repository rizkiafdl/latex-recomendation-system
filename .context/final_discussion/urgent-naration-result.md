# Narasi Hasil Penelitian

**Sistem Rekomendasi Faculty Supervisor Berbasis Semantic Similarity**
_Bahasa Indonesia — untuk keperluan penulisan BAB 4 dan ringkasan hasil_

---

## 1. Lingkungan Pengujian

Seluruh eksperimen dijalankan pada mesin Apple M4 Pro dengan 12 inti CPU dan 24 GB Unified Memory, sistem operasi macOS Sequoia 15. Stack perangkat lunak yang digunakan meliputi Python 3.12, Flask 3.1.0, SQLAlchemy 2.0.49 dengan SQLite, serta pustaka sentence-transformers, pandas, numpy, dan openpyxl.

---

## 2. Perbandingan Model Embedding

Tiga model embedding diuji pada konfigurasi identik: `extra_docs=True`, `group_bonus=False`, kapasitas standar (10–12 mahasiswa per supervisor).

| Model | MRR | Hit@1 | Hit@5 | nDCG@10 | Match Rate |
|---|---|---|---|---|---|
| **BAAI/bge-m3** (Run 26) | **0,711** | **56,0%** | **92,9%** | **0,779** | 0,530 |
| Qwen3-Embedding-0.6B (Run 32) | 0,617 | 47,0% | 85,1% | 0,698 | 0,512 |
| mE5-large-instruct (Run 38) | 0,672 | 50,6% | 90,5% | 0,748 | **0,583** |
| Baseline acak | ~0,23 | ~7% | ~36% | — | — |

BAAI/bge-m3 unggul pada seluruh metrik *retrieval*. Qwen3-0.6B konsisten berada di posisi ketiga. mE5-large-instruct menempati posisi kedua pada metrik *retrieval*, tetapi justru mencatat *Assignment Match Rate* tertinggi (0,583) — melampaui bge-m3 (0,530).

Perbedaan ini bukan karena mE5-large lebih baik secara semantik. Analisis sebaran skor kemiripan menjelaskan anomali ini:

| Model | Avg Similarity @1 | Avg Similarity (true) | Selisih |
|---|---|---|---|
| BAAI/bge-m3 | 0,674 | 0,645 | 0,030 |
| Qwen3-0.6B | 0,726 | 0,684 | 0,042 |
| mE5-large-instruct | 0,907 | 0,893 | **0,014** |

mE5-large menghasilkan skor yang sangat tinggi tetapi hampir seragam — rata-rata @1 adalah 0,907, sedangkan rata-rata untuk supervisor yang benar (*true*) adalah 0,893, hanya berbeda 0,014. Model ini menganggap hampir semua pasangan mahasiswa–supervisor "sangat mirip," sehingga kehilangan kemampuan diskriminatif untuk *ranking*. Akibatnya, penugasan lebih banyak ditentukan oleh kapasitas slot daripada urutan preferensi semantik — dan kebetulan lebih cocok dengan pola distribusi *ground truth* yang memang tidak murni berbasis keahlian.

Kesimpulannya: metrik *retrieval* (MRR, Hit@K, nDCG) adalah acuan utama pemilihan model. *Assignment Match Rate* yang tinggi tanpa didukung metrik *retrieval* yang baik justru mengindikasikan masalah kalibrasi skor, bukan kualitas rekomendasi yang sesungguhnya. **BAAI/bge-m3 adalah model terbaik untuk sistem ini.**

---

## 3. Pengaruh Parameter `extra_docs`

Penambahan data historis bimbingan ke profil supervisor (`extra_docs=True`) memberikan dampak yang berlawanan tergantung model.

| Model | extra_docs=True (Hit@1) | extra_docs=False (Hit@1) | Selisih |
|---|---|---|---|
| BAAI/bge-m3 | 67,8% | 62,0% | +5,8 pp |
| Qwen3-0.6B | 49,1% | 50,9% | −1,8 pp |
| mE5-large-instruct | 54,4% | 60,8% | **−6,4 pp** |

Untuk bge-m3, profil yang lebih kaya menghasilkan representasi supervisor yang lebih diskriminatif — wajar mengingat model ini dirancang untuk *dense retrieval* multi-domain. Untuk mE5-large, data tambahan justru mengganggu ruang embedding yang sudah terkalibrasi, menurunkan *Hit@1* sebesar 6,4 poin persentase.

Rekomendasi: aktifkan `extra_docs=True` hanya untuk bge-m3.

---

## 4. Pengaruh Parameter `group_bonus`

*Group bonus* — mekanisme penambahan skor untuk mahasiswa dari perusahaan yang sama yang diarahkan ke supervisor berbeda — tidak mengubah peringkat rekomendasi pada ketiga model.

Ketika `extra_docs` dikontrol konstan, selisih *Hit@1* antara `group_bonus=True` dan `group_bonus=False` adalah nol untuk semua model. Besaran bonus yang teraplikasi hanya sekitar 0,008 — tidak cukup untuk menggeser urutan supervisor yang selisih skornya jauh lebih besar. Perbedaan yang tampak di lintas batch sebelumnya adalah artefak desain eksperimen: `group_bonus=True` selalu dipasangkan dengan `extra_docs=False` (variabel tidak diisolasi).

Fitur ini secara teknis berfungsi tetapi tidak menghasilkan dampak terukur terhadap kualitas *ranking*.

---

## 5. Konfigurasi Terbaik: Run 26

Berdasarkan seluruh 18 eksperimen, konfigurasi terbaik adalah **BAAI/bge-m3 dengan `extra_docs=True` dan `group_bonus=False`** (Run 26).

| Metrik | Nilai |
|---|---|
| MRR | 0,711 |
| Hit@1 | 56,0% |
| Hit@5 | 92,9% |
| nDCG@5 | 0,759 |
| nDCG@10 | 0,779 |
| Rata-rata peringkat | 2,17 |
| %Top-3 | 85,4% |

Sistem menempatkan supervisor yang benar di urutan pertama untuk 56% mahasiswa, dan di lima besar untuk 92,9% mahasiswa — jauh di atas baseline acak (MRR ~0,23, Hit@5 ~36%). Rata-rata peringkat 2,17 berarti supervisor yang dipilih EPC secara historis rata-rata muncul di posisi kedua daftar rekomendasi sistem.

*Assignment Match Rate* sebesar 0,530 menunjukkan bahwa 53% penugasan akhir sistem sesuai dengan data historis EPC. Angka ini perlu dibaca bersama konteks bahwa *ground truth* sendiri adalah hasil pemetaan manual yang tidak selalu mempertimbangkan kesesuaian keahlian (per BAB 1), sehingga *match rate* di atas 50% pada kondisi tersebut sudah bermakna.

---

## 6. Distribusi Beban Supervisor (Greedy Solver)

Seluruh 18 konfigurasi menghasilkan distribusi beban yang identik karena kapasitas diatur pada rentang yang sama (10–12 per supervisor, overflow diarahkan ke prioritas tertentu).

- Total mahasiswa ditugaskan: **171** (termasuk 3 Study Abroad; evaluasi metrik menggunakan 168)
- Minimum: 12 mahasiswa/supervisor
- Maksimum: 13 mahasiswa/supervisor
- Rata-rata: 12,21
- Standar deviasi: 0,426
- **Koefisien Gini: 0,0138**

Dari 14 supervisor, 3 mendapat 13 mahasiswa dan 11 mendapat 12 mahasiswa. Gini 0,0138 mendekati nol sempurna — ini menyelesaikan masalah ketidakmerataan beban yang menjadi salah satu keluhan utama EPC di BAB 1, terlepas dari model mana yang digunakan.

---

## 7. Distribusi Peringkat Penugasan

Data dikumpulkan dari 3.078 slot penugasan (18 konfigurasi × 171 mahasiswa).

| Peringkat yang Ditetapkan | Jumlah | Persentase |
|---|---|---|
| 1 | 1.779 | 57,8% |
| 2 | 411 | 13,4% |
| 3 | 313 | 10,2% |
| 4 | 147 | 4,8% |
| 5 | 76 | 2,5% |
| 6 ke atas | 352 | 11,4% |

81,4% mahasiswa mendapat supervisor dari tiga teratas daftar rekomendasi. 11,4% yang jatuh di peringkat 6 ke atas bukan indikasi kegagalan model — ini konsekuensi matematis dari batasan kapasitas: ketika supervisor berperingkat tertinggi sudah penuh, sistem harus memilih dari alternatif berikutnya.

---

## 8. Pengujian Fungsionalitas (Black Box)

Pengujian dilakukan menggunakan metode Equivalence Partitioning dengan 56 skenario uji pada 9 halaman fungsional.

| Halaman | Skenario | Hasil |
|---|---|---|
| Login | 3 | Lulus |
| Register | 5 | Lulus |
| Dashboard | 10 | Lulus |
| Data Center | 7 | Lulus |
| Generate New Run | 8 | Lulus |
| Run History | 5 | Lulus |
| Supervisor Studio | 9 | Lulus |
| Run Detail | 7 | Lulus |
| Logout | 2 | Lulus |
| **Total** | **56** | **Semua Lulus** |

Setiap skenario diuji dengan partisi input valid dan tidak valid. Tidak ada skenario yang gagal.

---

## 9. Ringkasan Temuan

Tiga temuan utama penelitian ini:

**Pertama**, BAAI/bge-m3 adalah model terbaik untuk konteks ini. Ia menempatkan supervisor yang tepat di posisi pertama untuk 56% mahasiswa dan di lima besar untuk 92,9% — jauh melampaui baseline acak. Keunggulannya konsisten di seluruh metrik *retrieval*, bukan hanya satu indikator.

**Kedua**, `extra_docs` memperbaiki bge-m3 sebesar 5,8 poin persentase tetapi memperburuk mE5-large sebesar 6,4 poin. Sensitivitas model terhadap pengayaan profil ini adalah temuan substantif yang menunjukkan bahwa tidak ada konfigurasi tunggal yang optimal lintas model.

**Ketiga**, *greedy solver* menyelesaikan masalah distribusi beban sepenuhnya: Gini 0,0138, selisih maksimum hanya satu mahasiswa antar supervisor. Ini adalah hasil yang tidak bergantung pada pilihan model dan langsung menjawab keluhan ketidakmerataan beban yang diidentifikasi di BAB 1.

Satu temuan negatif yang jujur: `group_bonus` tidak mengubah peringkat rekomendasi secara terukur. Fitur ini ada dalam sistem, tetapi manfaatnya pada *ranking* tidak terbukti dari data.