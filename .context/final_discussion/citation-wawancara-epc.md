# Citation: Wawancara EPC (13 Desember 2025)

**Pertanyaan yang dijawab dokumen ini:**
> Kutipan wawancara bersama Margareth Setiawan, Yulianto, dan Mila Andria Savitri digunakan untuk apa dalam penelitian ini?

---

## Jawaban Singkat

Wawancara itu dipakai untuk **membuktikan bahwa masalah yang diangkat memang nyata** — bukan untuk mendefinisikan kebutuhan data sistem.

---

## Posisi dalam Skripsi

Kutipan ini muncul **hanya satu kali**, di **Latar Belakang BAB 1** (bab1.tex, baris 11). Tidak ada satu pun bab lain yang merujuk wawancara ini.

---

## Apa yang Diambil dari Wawancara

Empat fakta kondisi lapangan:

1. Pemetaan mahasiswa ke Faculty Supervisor dilakukan manual menggunakan *spreadsheet*
2. Proses ini butuh 1–3 minggu per *batch*
3. Revisi bisa terjadi hingga 20 kali per *batch*
4. Penugasan supervisor dilakukan secara acak, tanpa pertimbangan keahlian

Keempat poin itu dipakai untuk menjustifikasi *problem statement* — bukan untuk menetapkan data input sistem.

---

## Apa yang BUKAN Fungsi Wawancara Ini

- Bukan sumber kebutuhan data sistem
- Bukan yang menentukan kolom *input* (`track`, `position_topic`, `work_schema`)
- Bukan dasar kebutuhan fungsional

---

## Di Mana Kebutuhan Data Sebenarnya Didefinisikan

| Lokasi | Isi |
|--------|-----|
| BAB 1 Ruang Lingkup poin 3 | Data dibatasi pada *batch* 2026: data mahasiswa, profil Faculty Supervisor, dan data penugasan institusional sebagai *ground truth* |
| BAB 3 Analisis Data | Tiga kolom teks mahasiswa (`track`, `position_topic`, `work_schema`) dan `profile_keywords` dosen yang masuk ke proses *embedding* |
| BAB 3 Analisis Kebutuhan Fungsional | Tujuh kebutuhan fungsional sistem |

---

## Cara Menjawab Jika Ditanya

> *"Jelaskan kebutuhan data yang dibutuhkan — dan apakah wawancara EPC menjadi sumbernya?"*

**Jawaban yang tepat:**

Wawancara dengan EPC pada 13 Desember 2025 hanya mendeskripsikan **kondisi lapangan saat ini** di bagian Latar Belakang (Section 1.1) — bukan mendefinisikan kebutuhan data sistem, dan bukan pula menjadi sumber Rumusan Masalah.

Rumusan Masalah (Section 1.2) adalah tiga RQ formal yang diturunkan dari keseluruhan narasi Latar Belakang — termasuk hasil survei (baris 13), data pertumbuhan mahasiswa (Gambar 1.1), dan tinjauan literatur (baris 25). Interview hanya salah satu dari beberapa sumber yang membangun narasi itu. Interview tidak dikutip di Section 1.2 sama sekali.

Kebutuhan data sistem didefinisikan di BAB 3: tiga kolom teks mahasiswa yang dijadikan dokumen *embedding* (`track`, `position_topic`, `work_schema`), satu dokumen profil per dosen dari `profile_keywords`, dan data penugasan batch 2026 sebagai *ground truth* evaluasi. Sumbernya adalah analisis kebutuhan sistem, bukan wawancara.

---

*Dibuat: 2026-06-21 | Relevan untuk: sidang/viva, pertanyaan penguji BAB 1–3*
