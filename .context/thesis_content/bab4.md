# 4.2 Hasil

---

## 4.2.1 Evaluasi Model

Pada tahap ini dilakukan evaluasi terhadap tiga model *embedding* yang menjadi kandidat untuk komponen perhitungan kemiripan semantik: **bge-m3**, **qwen3-0.6b**, dan **multilingual-e5-large** (me5-large). Seluruh model dievaluasi menggunakan *pipeline*, dataset, serta konfigurasi kapasitas yang identik agar perbandingan bersifat adil. Evaluasi dilakukan pada **168 dari 171 mahasiswa**; sebanyak 3 mahasiswa dikecualikan karena tidak memiliki nilai `current_supervisor_code` yang valid sebagai *ground truth*. Jumlah dosen pembimbing yang aktif adalah **14 orang** dengan kapasitas individual sebesar 12–13 mahasiswa.

Setiap model dievaluasi pada beberapa kombinasi konfigurasi sistem (total 18 *run*, Run 26–43), dengan tiga dimensi konfigurasi yang divariasikan:

- **`extra_docs`**: apakah dokumen tambahan (publikasi, profil penelitian) diikutsertakan dalam representasi dosen.
- **`group_bonus`**: apakah skor kemiripan mendapat bonus berdasarkan kecocokan kelompok riset.
- **`capacity_priority_codes`**: apakah urutan pengisian slot mengutamakan kode dosen tertentu.

Untuk memastikan keterulangan (*reproducibility*), seluruh proses dibuat deterministik sehingga eksekusi berulang menghasilkan keluaran yang identik.

Pengukuran dilakukan pada dua tingkat yang saling melengkapi:

- **Metrik *retrieval* (sebelum *solver*):** MRR, Hit@1, Hit@5, NDCG@5, NDCG@10, dan rata-rata peringkat (*avg_rank*). Metrik ini mengukur kualitas pemeringkatan dosen pembimbing sebelum proses alokasi kapasitas dijalankan.
- **Metrik *assignment* (setelah *solver*):** *Assignment Match Rate*. Metrik ini mengukur kesesuaian hasil penugasan akhir terhadap penugasan historis admin.

Hasil agregasi per model disajikan pada **Tabel 4.3**, sedangkan konfigurasi terbaik per model ditunjukkan pada **Tabel 4.4**.

**Tabel 4.3 Rata-rata Metrik Evaluasi per Model Embedding (Run 26–43)**

| Model | MRR | Hit@1 | Hit@5 | NDCG@5 | NDCG@10 | Avg Rank | Match Rate |
|---|---|---|---|---|---|---|---|
| **bge-m3** | **0,660** | **0,512** | **0,861** | **0,698** | **0,731** | **2,748** | 0,505 |
| me5-large | 0,625 | 0,458 | 0,853 | 0,668 | 0,708 | 2,889 | **0,512** |
| qwen3-0.6b | 0,576 | 0,439 | 0,788 | 0,606 | 0,665 | 3,331 | 0,456 |
| *Random baseline* (M=14) | ~0,23 | ~0,07 | ~0,36 | ~0,21 | — | ~7,5 | — |

**Tabel 4.4 Konfigurasi Terbaik per Model**

| Run | Model | extra_docs | MRR | Hit@1 | Hit@5 | NDCG@5 | NDCG@10 | Avg Rank | Match Rate |
|---|---|---|---|---|---|---|---|---|---|---|
| **26** | **bge-m3** | True | **0,711** | **0,560** | **0,929** | **0,759** | **0,779** | **2,17** | 0,530 |
| 38 | me5-large | True | 0,672 | 0,506 | 0,905 | 0,721 | 0,748 | 2,43 | **0,583** |
| 32 | qwen3-0.6b | True | 0,617 | 0,470 | 0,851 | 0,660 | 0,698 | 2,95 | 0,512 |

Seluruh model berada jauh di atas *random baseline*, yang menunjukkan bahwa pendekatan kemiripan semantik memberikan sinyal pencocokan yang nyata.

Untuk keperluan analisis lebih lanjut, nilai rata-rata skor kemiripan disajikan pada **Tabel 4.5**.

**Tabel 4.5 Rata-rata Skor Kemiripan dan Sebaran per Model**

| Model | Avg Similarity (assigned) | delta_mean (jarak ke rank-1) | n_displaced > 0,15 |
|---|---|---|---|
| **bge-m3** | 0,634 | 0,025 | 15 (per run, konfigurasi edocs=True) |
| me5-large | 0,895 | **0,013** | **0** |
| qwen3-0.6b | 0,700 | 0,043 | hingga 15 per run |

---

## 4.2.2 Pengaruh Konfigurasi Sistem

Dengan 18 *run* yang memvariasikan tiga dimensi konfigurasi, dapat dianalisis kontribusi masing-masing parameter terhadap kualitas evaluasi.

**Pengaruh `extra_docs`**

Penggunaan dokumen tambahan (`extra_docs=True`) merupakan faktor konfigurasi dengan dampak terbesar pada seluruh batch ini. Dibandingkan tanpa dokumen tambahan, konfigurasi ini meningkatkan MRR dari 0,597 menjadi 0,667 (+0,070), Hit@1 dari 0,448 menjadi 0,512 (+0,064), dan memperbaiki rata-rata peringkat dari 3,23 menjadi 2,52. Hasil ini konsisten di seluruh model dan menunjukkan bahwa representasi dosen yang lebih kaya secara dokumen meningkatkan kemampuan diskriminasi *embedding* secara signifikan.

**Tabel 4.6 Pengaruh `extra_docs` terhadap Metrik Evaluasi**

| extra_docs | MRR | Hit@1 | Hit@5 | NDCG@5 | Avg Rank | Match Rate |
|---|---|---|---|---|---|---|
| **True** | **0,667** | **0,512** | **0,895** | **0,713** | **2,516** | **0,543** |
| False | 0,597 | 0,448 | 0,804 | 0,629 | 3,226 | 0,465 |

**Pengaruh `capacity_priority_codes`**

Parameter `capacity_priority_codes` tidak memiliki pengaruh terhadap metrik *retrieval*: nilai MRR, Hit, dan NDCG identik pada kondisi aktif maupun tidak aktif. Perbedaan *match_rate* yang timbul hanya sebesar +0,002, yang berada dalam batas *noise* statistis. Parameter ini hanya memengaruhi urutan pengisian slot alokasi pada level *solver*, bukan kualitas pemeringkatan.

**`group_bonus` dan Konfundasi dengan `extra_docs`**

Dalam batch ini, `group_bonus=True` selalu bersamaan dengan `extra_docs=False` — kedua variabel tidak pernah divariasikan secara independen. Akibatnya, perbedaan metrik yang tampak antara kondisi `group_bonus=True` dan `False` sepenuhnya dapat dijelaskan oleh efek `extra_docs`, bukan oleh *bonus* kelompok itu sendiri. Analisis terpisah dengan variasi terkontrol diperlukan untuk mengisolasi kontribusi `group_bonus`.

---

## 4.2.3 Distribusi Beban Dosen Pembimbing

Salah satu tujuan sistem adalah memastikan beban dosen pembimbing terdistribusi secara adil. Evaluasi terhadap 18 *run* menunjukkan bahwa distribusi beban bersifat **invariant terhadap seluruh dimensi konfigurasi**: model *embedding*, `group_bonus`, `extra_docs`, maupun `capacity_priority_codes` tidak mengubah statistik distribusi secara agregat.

**Tabel 4.7 Statistik Distribusi Beban Dosen Pembimbing (identik pada seluruh 18 run)**

| Metrik | Nilai |
|---|---|
| Minimum mahasiswa per dosen | 12 |
| Maksimum mahasiswa per dosen | 13 |
| Rata-rata | 12,21 |
| Standar deviasi | 0,41 |
| Rentang (maks − min) | 1 |
| Jumlah dosen di kapasitas maks (13) | 3 dari 14 |
| Koefisien Gini | **0,014** |

Koefisien Gini sebesar 0,014 menunjukkan distribusi yang hampir seragam sempurna. Kondisi ini secara matematis ditentukan oleh rasio jumlah mahasiswa terhadap dosen: 171 mahasiswa ÷ 14 dosen = 12,14, sehingga alokator harus memberikan 12 mahasiswa kepada 11 dosen dan 13 mahasiswa kepada 3 dosen (3×13 + 11×12 = 171). Tiga dosen yang secara konsisten mendapatkan 13 mahasiswa pada seluruh *run* adalah Dr. Abdul Haris Rangkuti (D2211), Dr. Boby Siswanto (D5918), dan Dr. Johan Muliadi Kerta (D1749).

Hasil ini merupakan properti dari *solver* alokasi kapasitas, bukan dari model *embedding* — dan membuktikan bahwa sistem mencapai distribusi beban yang optimal secara matematis terlepas dari konfigurasi *embedding* yang digunakan.

---

## 4.2.4 Distribusi Peringkat Penugasan

Selain kesesuaian dengan data historis, kualitas sistem juga diukur dari seberapa banyak mahasiswa yang berhasil ditempatkan pada dosen dengan peringkat kemiripan tertinggi. Analisis terhadap 3.078 data penugasan (18 *run* × 168 mahasiswa) menunjukkan distribusi peringkat sebagai berikut:

**Tabel 4.8 Distribusi Peringkat Penugasan (gabungan 18 run)**

| Peringkat yang Ditetapkan | Jumlah | Persentase |
|---|---|---|
| Peringkat 1 | 1.779 | **57,8%** |
| Peringkat 2 | 411 | 13,4% |
| Peringkat 3 | 313 | 10,2% |
| Peringkat 4 | 147 | 4,8% |
| Peringkat 5 | 76 | 2,5% |
| Peringkat > 5 | 352 | **11,4%** |

Secara keseluruhan, 57,8% mahasiswa berhasil ditempatkan pada dosen pembimbing dengan peringkat kemiripan tertinggi (peringkat 1). Sebanyak 11,4% mahasiswa ditempatkan di luar peringkat 5; kondisi ini merupakan konsekuensi dari batasan kapasitas — ketika seluruh slot pada lima dosen teratas telah penuh, alokator wajib menggunakan dosen pada peringkat lebih rendah.

Perbandingan distribusi peringkat antar model disajikan berikut:

| Model | % Peringkat 1 | % Top-3 | Avg Rank |
|---|---|---|---|
| **bge-m3** | **64,1%** | **85,8%** | **2,39** |
| me5-large | 58,7% | 83,5% | 2,84 |
| qwen3-0.6b | 50,6% | 74,7% | 2,89 |

Model bge-m3 menghasilkan penempatan terbaik: 64,1% mahasiswa mendapat dosen peringkat-1 dengan rata-rata peringkat 2,39. Model me5-large menghasilkan delta skor terendah (*delta_mean* = 0,013) dan nol mahasiswa yang ditempatkan lebih dari 0,15 poin di bawah skor terbaik mereka, menunjukkan distribusi skor yang sangat rapat meskipun lebih sedikit mahasiswa mencapai peringkat-1.

---

## 4.2.5 Analisis Model dan Pemilihan Model Akhir

Berdasarkan hasil evaluasi pada subbab-subbab sebelumnya, **bge-m3** unggul pada seluruh metrik *retrieval* utama: MRR rata-rata 0,660, Hit@1 0,512, Hit@5 0,861, dan rata-rata peringkat 2,748 — terbaik di antara seluruh kandidat. Pada konfigurasi optimalnya (Run 26, `extra_docs=True`), model ini mencapai MRR 0,711, Hit@1 0,560, dan Hit@5 0,929. Keunggulan ini konsisten di setiap metrik, bukan hanya pada satu indikator, sehingga menunjukkan kualitas pemeringkatan yang lebih baik secara menyeluruh.

Temuan menarik muncul pada **me5-large**. Model ini memiliki *match_rate* tertinggi (0,583 pada Run 38) dan menghasilkan distribusi skor yang sangat rapat (*delta_mean* = 0,013, nol mahasiswa dengan perpindahan tinggi). Namun, skor kemiripan absolutnya yang tinggi (rata-rata 0,895) justru bersifat kurang diskriminatif — hampir seluruh pasangan mahasiswa–dosen dinilai "sangat mirip" sehingga perbedaan antar dosen menjadi sangat kecil. Tingginya *match_rate* tidak sepenuhnya berasal dari kualitas pemeringkatan, melainkan juga dari distribusi skor yang datar yang secara kebetulan sejalan dengan pola alokasi kapasitas historis.

**qwen3-0.6b** menempati posisi terakhir pada metrik *retrieval* (MRR 0,576, Avg Rank 3,331) dan menghasilkan *match_rate* terendah (0,456). Hanya 50,6% mahasiswa yang mendapat dosen peringkat-1, dengan *displacement* tertinggi di antara ketiga model.

Temuan ini menegaskan prinsip penting dalam pemilihan model: **sebuah sistem rekomendasi dinilai dari kualitas pemeringkatannya** — kemampuan menempatkan dosen yang tepat pada posisi teratas — sehingga metrik *retrieval* menjadi acuan utama. *Assignment Match Rate* merupakan metrik hasil akhir yang bersifat melengkapi, namun apabila digunakan sendirian dapat memberikan gambaran yang menyesatkan, sebagaimana ditunjukkan oleh kasus me5-large.

Dengan pertimbangan tersebut, **bge-m3 dengan konfigurasi `extra_docs=True`** dipilih sebagai model dan konfigurasi akhir yang digunakan dalam sistem. Pemilihan ini didasarkan pada keunggulan menyeluruh pada metrik *retrieval* di seluruh konfigurasi yang diuji, serta konfirmasi bahwa penggunaan dokumen tambahan (`extra_docs=True`) merupakan faktor konfigurasi dengan dampak paling signifikan (+0,070 MRR) dan konsisten di seluruh model. Selain itu, hasil model ini telah diverifikasi bersifat deterministik dan dapat direproduksi secara identik pada eksekusi berulang, sehingga angka yang dilaporkan dapat dipertanggungjawabkan.
