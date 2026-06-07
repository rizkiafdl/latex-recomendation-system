# Final Draft Gap Analysis
> Status snapshot per 2026-06-07. Update setiap kali gap diselesaikan.
> Referensi utama: thesis_content_map.md untuk isi lengkap per chapter.

---

## Status Keseluruhan

| Chapter | Status Konten | Gap Kritis |
|---|---|---|
| BAB 1 | ✅ Written | — |
| BAB 2 | ⚠️ Mostly written | 3 studi hallucinated, ref.bib kosong |
| BAB 3 | ✅ Written & consistent | — |
| BAB 4 | ✅ Written & updated | — |
| BAB 5 | ✅ Written | — |
| abstrak.tex | ✅ Written | — |
| abstract.tex | ⚠️ Disabled | `% \include{abstract}` di-comment di Skripsi.tex |

---

## GAP 1 — SUS Evaluation ✅ RESOLVED (2026-06-07)
**Keputusan: Dihapus (Opsi B)**

Yang dihapus dari bab2.tex:
- Sub-section `\subsection{System Usability Scale (SUS)}` beserta seluruh isinya
- Kalimat "Yang kedua adalah Evaluasi Usability (SUS)..." dari paragraf intro
- "dan System Usability Scale (SUS)" dari kalimat penutup Penelitian Terkait

---

## GAP 2 — Figure Captions BAB 4 §4.2.3 ✅ RESOLVED
**Delapan screenshot sudah memiliki `\caption` dan `\label`:**
- `fig:login` → "Tampilan Halaman Login"
- `fig:register` → "Tampilan Halaman Register"
- `fig:dashboard` → "Tampilan Halaman Dashboard"
- `fig:datacenter` → "Tampilan Halaman Data Center"
- `fig:runhistory` → "Tampilan Halaman Run History"
- `fig:rundetails` → "Tampilan Halaman Run Details"
- `fig:supervisorstudio` → "Tampilan Halaman Supervisor Studio"
- `fig:rulestudio` → "Tampilan Halaman Rules Studio"

---

## GAP 3 — Inkonsistensi BAB 3 ↔ BAB 4: Model Kandidat ✅ RESOLVED (2026-06-07)
Model di BAB 3 §3.2.3 sekarang sesuai dengan yang digunakan di BAB 4:
- BAAI/bge-m3, Qwen/Qwen3-Embedding-0.6B, intfloat/multilingual-e5-large-instruct

---

## GAP 4 — Inkonsistensi BAB 3 ↔ BAB 4: Metrik Evaluasi ✅ RESOLVED (2026-06-07)
BAB 3 §3.2.3 sekarang mencantumkan: MRR, Hit@1, Hit@5, NDCG@5, NDCG@10, Avg Rank, Assignment Match Rate.

---

## GAP 5 — Rules Studio Prose & Testing Table ✅ RESOLVED (2026-06-07)
- Deskripsi halaman Rules Studio di §4.2.3 sudah ditulis ulang (bukan copy-paste dari Supervisor Studio)
- Tabel pengujian Rules Studio (tab4.10) sudah diganti dengan skenario yang benar:
  threshold edit, invalid threshold, label description update, preview score, save config, navigate

---

## GAP 6 — BAB 2 Penelitian Terkait: 3 Studi Hallucinated ❌ OPEN
**Severity: KRITIS (academic integrity)**

Tiga baris longtable terindikasi hallucinated — tidak ditemukan di web search:

| Studi | Masalah |
|---|---|
| Rahman et al. (2023) | Zero search results; kolom hasil = copy judul |
| Li et al. (2022) | Zero search results; dataset "Universitas Hasyim" tidak dapat diverifikasi |
| Zhang et al. (2021) | Zero search results; kolom hasil = copy judul |

**2 paper pengganti nyata sudah ditemukan (dari citation-audit.md):**
- Hairani & Mujahid (2022) — SISTEMASI — 91.3% accuracy
- Sabilillah et al. (2024) — Edumatic — 90% accuracy

**Resolusi yang diperlukan:**
- [ ] Cari 1 paper lagi (2021–2023, deep learning / transformer untuk supervisor recommendation)
- [ ] Ganti 3 baris hallucinated di bab2.tex longtable dengan paper nyata
- [ ] Isi kolom hasil dengan data aktual (bukan copy judul)

---

## GAP 7 — "XYZ University": Placeholder atau Anonimisasi? ❌ OPEN
**Severity: SEDANG**

Muncul di: Awal_konfigurasi.tex, bab1.tex, abstrak.tex, bab5.tex.

**Resolusi yang diperlukan:**
- [ ] Konfirmasi dengan pembimbing: nama nyata atau tetap anonim
- [ ] Jika diganti: gunakan `replace_all` di setiap file terkait

---

## GAP 8 — Placeholder Nama Model ✅ RESOLVED (2026-06-07)
Semua placeholder `[LENGKAPI: nama model A/B/C]` sudah diisi:
- A = BAAI/bge-m3, B = Qwen/Qwen3-Embedding-0.6B, C = intfloat/multilingual-e5-large-instruct

---

## GAP 9 — Lampiran: Belum Ada ❌ OPEN
**Severity: SEDANG** (tergantung panduan BINUS)

- [ ] Cek panduan penulisan skripsi BINUS terbaru
- [ ] Tambahkan lampiran yang diperlukan (kuesioner, surat izin, sampel data, screenshot)

---

## GAP 10 — ref.bib Kosong & 0 \cite{} Command ❌ OPEN
**Severity: KRITIS (bibliography tidak akan ter-render)**

- `ref.bib` berisi 13 template placeholder (Peter Adams 1993 dll.) + 2 paper tidak relevan — 0 entry usable
- Tidak ada satu pun `\cite{}` di seluruh .tex — semua 30+ sitasi ditulis sebagai teks inline
- Jika compile sekarang: bibliography halaman kosong

**Resolusi yang diperlukan:**
- [ ] Buat entri BibTeX untuk seluruh 30+ sitasi (Mikolov 2013, Vaswani 2017, Devlin 2019, dst.)
- [ ] Ganti semua sitasi inline menjadi `\cite{key}` di bab1.tex–bab5.tex
- [ ] Verifikasi compile: `pdflatex → bibtex → pdflatex × 2`

---

## GAP 11 — BAB 4 Data Evaluasi: Old Batch → New Batch ✅ RESOLVED (2026-06-07)
Tabel evaluasi bab4.tex diperbarui dari data lama (Run 5/6/9) ke batch terbaru (`batch_20260607_163454`, Run 8/14/20):

| Model | Run lama | Run baru | Perubahan metrik |
|---|---|---|---|
| BAAI/bge-m3 | Run 5 | Run 8 | Hit@5: 0.804→0.798, avg_rank: 3.32→3.33 |
| Qwen3 | Run 6 | Run 14 | MRR: 0.474→0.478, match_rate: 0.357→0.423 |
| mE5-large | Run 9 | Run 20 | match_rate: 0.381→0.399 |

Tabel similarity scores juga diperbarui dari delta_mean baru.

---

## GAP 12 — BAB 4 Inversion Attribution Salah ✅ RESOLVED (2026-06-07)
§4.2.2 sebelumnya menyebut mE5-large sebagai model dengan match_rate tertinggi.
Data batch baru menunjukkan Qwen3-0.6B yang memiliki match_rate tertinggi (0.423).
Seluruh analisis §4.2.2 sudah ditulis ulang sesuai data baru.

---

## GAP 13 — BAB 4 Sections Baru: Toggle Analysis, Load, Rank ✅ RESOLVED (2026-06-07)
Tiga subbab baru ditambahkan ke bab4.tex berdasarkan `analysis-finding.md`:

- **§4.2.3 Analisis Pengaruh Parameter Konfigurasi** — tabel extra_docs per model, tabel group_bonus per model, tabel konfigurasi terbaik (Run 8)
- **§4.2.4 Distribusi Beban Pembimbing** — tabel Gini=0.0138, semua 18 konfigurasi identik
- **§4.2.5 Distribusi Peringkat Penugasan** — tabel distribusi bimodal (45.4% rank-1, dip 2–3, naik lagi di 4)

---

## GAP 14 — BAB 3 Greedy Solver Description Salah ✅ RESOLVED (2026-06-07)
§3.2.3.6 (Validasi Slot) sebelumnya mendeskripsikan greedy naif (mahasiswa diproses satu per satu berdasarkan skor). Implementasi aktual adalah 2-phase batch greedy:
1. Init: semua mahasiswa ke argmax
2. Phase 1: kurangi overfull (min penalty)
3. Phase 2: isi underfull (min penalty)

Teks sudah ditulis ulang. Kalimat "mahasiswa tidak dialokasikan jika top-5 penuh" (salah) dihapus — solver raise RuntimeError jika constraint tidak terpenuhi.

---

## GAP 15 — BAB 4 Login/Register Testing: "Email" → "Username" ✅ RESOLVED (2026-06-07)
Sistem autentikasi menggunakan username (bukan email). Tabel skenario Login dan Register di bab4.tex sudah diperbarui. Register scenarios juga direvisi agar sesuai validasi aktual (password≠confirm, username sudah dipakai).

---

## GAP 16 — BAB 1 Figure 1.1 Missing ✅ RESOLVED (2026-06-07)
Grafik "Data Mahasiswa 2021--2025" ditambahkan:
- Image: `pic/pertumbuhan-mahasiswa.png`
- Label: `fig:fig1.1`
- \ref{} ditambahkan pada prose di §1.1 ("sebagaimana terlihat pada Gambar~\ref{fig:fig1.1}")

---

## Checklist Final Draft

**Resolved:**
- [x] GAP 1 — SUS dihapus dari BAB 2 ✅
- [x] GAP 2 — Figure captions 8 gambar BAB 4 ✅
- [x] GAP 3 — Model kandidat BAB 3 konsisten dengan BAB 4 ✅
- [x] GAP 4 — Metrik evaluasi BAB 3 konsisten dengan BAB 4 ✅
- [x] GAP 5 — Rules Studio prose & testing table ✅
- [x] GAP 8 — Placeholder nama model A/B/C ✅
- [x] GAP 11 — Data evaluasi diperbarui ke batch terbaru ✅
- [x] GAP 12 — Inversion attribution diperbaiki (mE5→Qwen3) ✅
- [x] GAP 13 — Tiga subbab analisis baru (toggle, load, rank) ✅
- [x] GAP 14 — BAB 3 greedy solver description diperbaiki ✅
- [x] GAP 15 — Login/Register testing: email→username ✅

**Still Open:**
- [ ] GAP 6 — 3 studi hallucinated di BAB 2 (butuh penggantian + 1 paper lagi)
- [ ] GAP 7 — "XYZ University" (keputusan pembimbing diperlukan)
- [ ] GAP 9 — Lampiran (cek panduan BINUS)
- [ ] GAP 10 — ref.bib kosong + 0 `\cite{}` command ← **prioritas tertinggi**
- [x] GAP 16 — BAB 1 Figure 1.1 (grafik pertumbuhan mahasiswa) ✅
