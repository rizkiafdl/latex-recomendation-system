# List of Concerns — Thesis Defense
> Potensi kelemahan, keterbatasan desain, dan hal yang perlu disiapkan jawabannya saat sidang.

---

## C1 — Konfounding Desain Eksperimen: `group_bonus` × `extra_docs`

**Masalah:**  
`group_bonus=True` **selalu** dipasangkan dengan `extra_docs=False` di seluruh 18 run. Keduanya tidak pernah divariasikan secara independen.

| Kombinasi | Diuji? |
|---|---|
| group_bonus=False, extra_docs=True | ✅ Ya (Run 26/27/32/33/38/39) |
| group_bonus=False, extra_docs=False | ✅ Ya (Run 30/31/36/37/42/43) |
| group_bonus=True, extra_docs=False | ✅ Ya (Run 28/29/34/35/40/41) |
| **group_bonus=True, extra_docs=True** | ❌ **Tidak pernah diuji** |

**Implikasi:**  
- Klaim "group_bonus tidak berpengaruh pada ranking" hanya valid pada kondisi `extra_docs=False` (profil supervisor tipis — hanya `profile_keywords`).
- Tidak bisa disimpulkan apakah group_bonus efektif atau tidak ketika profil supervisor kaya (`extra_docs=True`).
- Perbedaan performa yang tampak antar *batch* eksperimen sebelumnya (bukan batch ini) yang dikaitkan ke group_bonus sebenarnya adalah efek `extra_docs`.

**Jawaban jika ditanya penguji:**  
*"Ini merupakan keterbatasan desain eksperimen pada batch run ini. Kedua variabel tidak pernah diisolasi secara independen sehingga klaim tentang group_bonus dibatasi pada kondisi extra_docs=False. Sebagai pengembangan selanjutnya, eksperimen dengan keempat kombinasi penuh perlu dilakukan."*

---

## C2 — Kualitas Ground Truth

**Masalah:**  
Ground truth berasal dari pemetaan manual EPC yang di BAB 1 sendiri dideskripsikan sebagai: *"penentuan acak tanpa mempertimbangkan kesesuaian bidang keahlian."* Artinya ground truth bukan representasi "pasangan ideal" — hanya representasi "apa yang sudah terjadi secara historis."

**Implikasi:**  
- Match Rate 53% tidak bisa diinterpretasikan sebagai "sistem hanya benar 53%." Sistem bisa saja merekomendasikan supervisor yang lebih tepat secara semantik, tapi berbeda dari histori EPC.
- Evaluasi retrieval (MRR, Hit@K, nDCG) memakai ground truth yang sama — jika ground truth-nya tidak ideal, metrik ini pun merepresentasikan "seberapa mirip sistem dengan keputusan EPC", bukan "seberapa tepat sistem secara absolut."

**Jawaban jika ditanya penguji:**  
*"Keterbatasan ini diakui. Ground truth yang tersedia adalah satu-satunya referensi institusional yang ada secara operasional. Evaluasi dengan expert judgment atau studi longitudinal akan memberikan gambaran yang lebih representatif — ini sudah masuk saran BAB 5 poin 4 dan 6."*

---

## C3 — Evaluasi Hanya Satu Batch, Satu Program Studi

**Masalah:**  
Data evaluasi hanya dari batch 2026, program studi Computer Science, XYZ Bandung: 168 mahasiswa valid, 14 supervisor.

**Implikasi:**  
- Tidak bisa diklaim model berperforma sama di:
  - Batch berbeda (data supervisor/mahasiswa berubah tiap tahun)
  - Fakultas/program studi lain (karakteristik data berbeda)
  - Skala lebih besar (ratusan supervisor)
- Hasil sangat tergantung kualitas `profile_keywords` yang diisi EPC — jika keywords minim atau tidak representatif, hasil rekomendasi akan buruk terlepas dari model apapun.

**Jawaban jika ditanya penguji:**  
*"Ini keterbatasan yang diakui di ruang lingkup BAB 1 dan saran BAB 5 poin 7 (replikasi ke fakultas lain). Penelitian ini adalah proof-of-concept pada satu konteks spesifik."*

---

## C4 — Tidak Ada Evaluasi Usabilitas (SUS) dengan EPC Nyata

**Masalah:**  
Pengujian sistem terbatas pada black box testing fungsional (56 skenario, equivalence partitioning). Tidak ada uji SUS atau user acceptance testing dengan EPC asli sebagai pengguna.

**Implikasi:**  
- Tidak bisa diklaim sistem "mudah digunakan" atau "diterima pengguna" — hanya bisa diklaim "fungsi berjalan sesuai spesifikasi."
- Ruang lingkup BAB 1 poin 9 memang sudah membatasi: *"Penelitian ini tidak mencakup evaluasi usabilitas atau penerimaan pengguna oleh EPC."*

**Jawaban jika ditanya penguji:**  
*"Sudah dibatasi di ruang lingkup BAB 1 dan masuk saran BAB 5 poin 4. Evaluasi SUS dengan EPC nyata adalah langkah validasi berikutnya."*

---

## C5 — Diskrepansi On-Premises (BAB 2) vs VPS Cloud (BAB 3)

**Masalah:**  
BAB 2 (Landasan Teori) mendefinisikan *on-premises deployment* sebagai sistem yang dijalankan di infrastruktur lokal dengan Docker container. Namun implementasi aktual di BAB 3 menggunakan VPS cloud (Tencent Cloud) untuk akses eksternal via port forwarding.

**Implikasi:**  
Minor inkonsistensi lintas bab. Penguji yang teliti bisa mempertanyakan ini.

**Jawaban jika ditanya penguji:**  
*"Konsep on-premises di BAB 2 merujuk pada arsitektur deployment yang tidak bergantung API eksternal — model dijalankan lokal, data tidak keluar ke layanan pihak ketiga. Akses eksternal melalui VPS adalah mekanisme networking untuk keperluan testing, bukan perubahan arsitektur deployment."*

---

## C6 — `cap_priority` Tidak Mengubah Distribusi Agregat

**Masalah:**  
Parameter `capacity_priority_codes` (mencakup D2211, D1749, D6184, D6826) dirancang untuk mengontrol supervisor mana yang mendapat overflow slot. Namun dalam praktiknya, 3 supervisor yang selalu mendapat 13 mahasiswa (D2211, D5918, D1749) tidak sepenuhnya dikendalikan oleh `cap_priority` — D5918 selalu mendapat slot ekstra meskipun tidak masuk priority list.

**Implikasi:**  
Parameter ini terbukti tidak berpengaruh pada distribusi metrik evaluasi maupun distribusi beban agregat (identik di semua run). Fungsinya terbatas pada *tie-breaking* internal allocator, bukan kontrol overflow yang efektif.

**Jawaban jika ditanya penguji:**  
*"Benar, cap_priority tidak mengubah distribusi beban agregat karena distribusi beban sudah ditentukan secara matematis oleh rasio mahasiswa-supervisor (171÷14). Parameter ini hanya mempengaruhi penugasan mahasiswa tertentu ke supervisor prioritas, bukan keseimbangan keseluruhan."*

---

## C7 — Batas Konteks mE5-large-instruct (512 token)

**Masalah:**  
intfloat/multilingual-e5-large-instruct hanya mendukung 512 token. Dokumen supervisor ketika `extra_docs=True` bisa panjang (historis bimbingan banyak mahasiswa + rekomendasi skripsi). Dokumen yang melebihi 512 token akan terpotong.

**Implikasi:**  
Ini menjelaskan sebagian mengapa mE5-large terdegradasi ketika `extra_docs=True` (Hit@1 turun 6,4 pp): dokumen yang terpotong menghasilkan representasi yang tidak lengkap dan merusak kalibrasi embedding.

**Jawaban jika ditanya penguji:**  
*"Ini adalah salah satu faktor yang berkontribusi pada degradasi mE5-large dengan extra_docs=True. Batas 512 token menjadi bottleneck ketika profil supervisor diperkaya — berbeda dengan bge-m3 (8K token) dan Qwen3 (32K token) yang mampu memuat seluruh dokumen."*

---

## C8 — group\_bonus Jarang Aktif Secara Teknis

**Masalah:**  
group\_bonus hanya aktif jika semua 3 syarat terpenuhi: (1) ≥2 mahasiswa se-perusahaan, (2) diversitas topik ≤6 token, (3) selisih skor 2 dosen terbaik ≥0,08. Kondisi ini mungkin jarang terpenuhi secara bersamaan — terutama syarat ke-3 untuk model dengan skor seragam seperti mE5-large.

**Implikasi:**  
Kontribusi bonus rata-rata hanya ~0,008 (terdeteksi di `gap(fin-sim)` Agent 3), tidak cukup untuk menggeser urutan ranking. Fitur ini ada dan berfungsi teknis, tapi dampaknya praktis nol pada kualitas rekomendasi.

**Jawaban jika ditanya penguji:**  
*"Group bonus berfungsi secara teknis — tercatat gap skor 0,008 ketika aktif. Namun kondisi aktivasinya cukup ketat sehingga dalam praktik, bonus tidak cukup besar untuk mengubah urutan ranking. Ini temuan negatif yang jujur dan sudah dilaporkan di BAB 4."*
