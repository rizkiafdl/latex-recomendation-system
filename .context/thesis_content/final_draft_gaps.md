# Final Draft Gap Analysis
> Status snapshot per 2026-06-07. Update setiap kali gap diselesaikan.
> Referensi utama: thesis_content_map.md untuk isi lengkap per chapter.

---

## Status Keseluruhan

| Chapter | Status Konten | Gap Kritis |
|---|---|---|
| BAB 1 | ✅ Written | — |
| BAB 2 | ⚠️ Mostly written | 3 studi tidak lengkap |
| BAB 3 | ⚠️ Written tapi inkonsisten | Model kandidat + metrik ≠ BAB 4 |
| BAB 4 | ⚠️ Partially written | Placeholder model, figure captions, Rules Studio salah |
| BAB 5 | ✅ Written (baru) | Satu placeholder nama model A |
| abstrak.tex | ✅ Written | — |
| abstract.tex | ⚠️ Disabled | `% \include{abstract}` di-comment di Skripsi.tex |

---

## GAP 1 — SUS Evaluation ✅ RESOLVED (2026-06-07)
**Keputusan: Dihapus (Opsi B)**

Yang dihapus dari bab2.tex:
- Sub-section `\subsection{System Usability Scale (SUS)}` beserta seluruh isinya (rumus, langkah scoring)
- Kalimat "Yang kedua adalah Evaluasi Usability (SUS)..." dari paragraf intro Evaluasi Sistem Rekomendasi
- "dan System Usability Scale (SUS)" dari kalimat penutup Penelitian Terkait

Framing evaluasi di BAB 2 sekarang: evaluasi otomatis berbasis metrik kuantitatif saja (MRR, Hit@K, nDCG@K, Match Rate).

---

## GAP 2 — Figure Captions BAB 4 §4.2.3: Semua Kosong
**Severity: KRITIS**

Delapan screenshot aplikasi web tidak memiliki `\caption` dan `\label`:
- `pic/login.png`
- `pic/register.png`
- `pic/dashboard.png`
- `pic/datacenter.png`
- `pic/runhistory.png`
- `pic/rundetails.png`
- `pic/supervisorstudio.png`
- `pic/rulestudio.png`

**Akibat:** Tidak muncul di Daftar Gambar, tidak bisa di-cross-reference dari teks.

**Resolusi:** Tambahkan `\caption{...}` dan `\label{fig:...}` pada setiap `\begin{figure}` di §4.2.3 bab4.tex.

**Format caption yang diusulkan:**
```
Gambar 4.1  Tampilan Halaman Login
Gambar 4.2  Tampilan Halaman Register
Gambar 4.3  Tampilan Halaman Dashboard
Gambar 4.4  Tampilan Halaman Data Center
Gambar 4.5  Tampilan Halaman Run History
Gambar 4.6  Tampilan Halaman Run Details
Gambar 4.7  Tampilan Halaman Supervisor Studio
Gambar 4.8  Tampilan Halaman Rules Studio
```

---

## GAP 3 — Inkonsistensi BAB 3 ↔ BAB 4: Model Kandidat
**Severity: KRITIS**

| | BAB 3 (direncanakan) | BAB 4 (aktual) |
|---|---|---|
| Model 1 | Qwen3-Embedding-0.6B | Qwen/Qwen3-Embedding-0.6B ✓ |
| Model 2 | EmbeddingGemma-300M | BAAI/bge-m3 ✗ |
| Model 3 | Jina-embeddings-v3 | intfloat/multilingual-e5-large-instruct ✗ |

**Resolusi:** Update BAB 3 §3.2.3 (sub-section "Kandidat Model Text Embedding") agar deskripsi ketiga model sesuai dengan yang benar-benar digunakan di BAB 4.

---

## GAP 4 — Inkonsistensi BAB 3 ↔ BAB 4: Metrik Evaluasi
**Severity: KRITIS**

| | BAB 3 (direncanakan) | BAB 4 (aktual) |
|---|---|---|
| Metrik retrieval | Recall@5, nDCG@5 | MRR, Hit@1, Hit@5, NDCG@5, NDCG@10, avg\_rank |
| Metrik assignment | (tidak disebutkan) | Assignment Match Rate |

**Resolusi:** Update BAB 3 §3.2.3 (sub-section "Strategi Evaluasi") agar mencantumkan metrik yang benar-benar digunakan.

---

## GAP 5 — Rules Studio Prose di BAB 4 §4.2.3: Copy-Paste Salah
**Severity: SEDANG**

Deskripsi halaman Rules Studio saat ini identik kata per kata dengan deskripsi Supervisor Studio.

**Yang seharusnya tertulis untuk Rules Studio:**
Halaman Rules Studio berfungsi mengonfigurasi parameter aturan yang digunakan dalam proses rekomendasi. Fitur utama meliputi pengaturan *cosine similarity threshold* per label semantik, pengelolaan deskripsi label yang menentukan pemetaan profil mahasiswa ke kategori penugasan tertentu (seperti `apple\_mobile` atau `binus\_bandung`), manajemen matriks afinitas (*boost value*) antara dosen dan label, serta pratinjau skor label. Perubahan pada halaman ini akan memicu invalidasi *cache* agar hasil run berikutnya mencerminkan konfigurasi terbaru.

**Resolusi:** Ganti teks duplikat di bab4.tex §4.2.3 Rules Studio dengan deskripsi di atas.

---

## GAP 6 — BAB 2 Penelitian Terkait: 3 Studi Tidak Lengkap
**Severity: SEDANG**

Tiga baris longtable tidak memiliki hasil/temuan:

| Studi | Yang Kurang |
|---|---|
| Rahman et al. (2023) | Hasil evaluasi / performa metrik |
| Li et al. (2022) | Hasil evaluasi / performa metrik |
| Zhang et al. (2021) | Hasil evaluasi / performa metrik |

**Resolusi:** Isi kolom hasil ketiga studi dari sumber aslinya (paper/referensi).

---

## GAP 7 — "XYZ University": Placeholder atau Anonimisasi?
**Severity: SEDANG**

Nama mitra universitas muncul sebagai "XYZ University" / "XYZ" di seluruh dokumen:
- Judul thesis (Awal_konfigurasi.tex)
- BAB 1 (Latar Belakang, Ruang Lingkup)
- abstrak.tex (Indonesia + Inggris)
- BAB 5 (Simpulan)

**Resolusi yang diperlukan:**
- [ ] Konfirmasi dengan pembimbing: nama nyata dicantumkan atau tetap dianonimkan
- [ ] Jika perlu diganti: gunakan `replace_all` di setiap file terkait

---

## GAP 8 — Placeholder Nama Model di BAB 4 & BAB 5
**Severity: SEDANG** (blocker untuk finalisasi)

Lokasi `[LENGKAPI: nama model A]` dan variannya:
- bab4.tex §4.2.1 — dalam paragraf + Table 4.2.1 (3 baris)
- bab4.tex §4.2.2 — dalam paragraf analisis
- bab4.tex §4.2.1 — `[LENGKAPI: jumlah model]` (kemungkinan "tiga")
- bab5.tex §5.1 — poin simpulan no. 3

**Resolusi:** Konfirmasi nama model untuk Run 5, 6, 9 lalu isi semua sekaligus.

---

## GAP 9 — Lampiran: Belum Ada
**Severity: SEDANG** (tergantung panduan BINUS)

Tidak terdeteksi file lampiran di repository. BINUS umumnya mensyaratkan lampiran untuk:
- Kuesioner / instrumen evaluasi
- Surat izin penelitian / pengambilan data
- Data mentah atau sampel dataset
- Dokumentasi tambahan (screenshot tambahan, hasil ekspor, dll.)

**Resolusi:** Cek panduan penulisan skripsi BINUS terbaru dan tambahkan lampiran yang diperlukan.

---

## Checklist Final Draft

- [x] GAP 1 — SUS dihapus dari BAB 2 ✅
- [x] GAP 2 — Tambah caption + label ke 8 figure di BAB 4 §4.2.3 ✅
- [x] GAP 3 — Update model kandidat di BAB 3 §3.2.3 ✅ (EmbeddingGemma→BAAI/bge-m3, Jina→multilingual-e5-large-instruct)
- [x] GAP 4 — Update metrik evaluasi di BAB 3 §3.2.3 ✅ (Recall@5/nDCG@5 → MRR, Hit@1/5, NDCG@5/10, Avg Rank, Assignment Match Rate)
- [x] GAP 5 — Tulis ulang Rules Studio prose di BAB 4 §4.2.3 ✅
- [ ] GAP 6 — Lengkapi 3 studi di BAB 2 longtable
- [ ] GAP 7 — Klarifikasi dan resolve "XYZ University"
- [x] GAP 8 — Isi semua placeholder nama model ✅ (A=BAAI/bge-m3, B=Qwen/Qwen3-Embedding-0.6B, C=intfloat/multilingual-e5-large-instruct)
- [ ] GAP 9 — Cek dan tambahkan lampiran sesuai panduan BINUS
