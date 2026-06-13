# Activity Diagram Descriptions — Sistem Rekomendasi Faculty Supervisor

Derived from: `.context/Diagram/UseCaseDiagramDescription.md`
Date: 2026-06-13 | Last updated: 2026-06-13

Each activity diagram uses **two swimlanes**: **EPC** (actor) and **Sistem** (the application).
UML activity diagram notation: start node (filled circle) → actions → decision diamonds → end node (bullseye).

TikZ style rules: [[ActivityDiagramTikzRules.md]]

Priority guide for thesis inclusion:
- ★★★ Must include — core system logic, non-trivial flow
- ★★  Should include — meaningful branching or multi-step flow
- ★   Optional — trivial or standard pattern

Generation status:
- ✅ Done — TikZ generated, inserted into bab3.tex
- ⬜ Pending — not yet generated

---

## AD-01 · Register ★★ ✅

**TikZ file:** `.context/Diagram/activity-register.tex` · **label:** `fig:ad01-register`

**Swimlanes:** EPC | Sistem

| Step | Lane | Action |
|------|------|--------|
| 1 | EPC | Mengakses halaman Register |
| 2 | EPC | Mengisi form (username, password, konfirmasi password, full name) |
| 3 | EPC | Menekan tombol Register |
| 4 | Sistem | Memvalidasi panjang username (≥ 3 karakter) |
| 5 | Sistem | **[Tidak valid]** Menampilkan pesan error → kembali ke langkah 2 |
| 6 | Sistem | Memvalidasi panjang password (≥ 6 karakter) |
| 7 | Sistem | **[Tidak valid]** Menampilkan pesan error → kembali ke langkah 2 |
| 8 | Sistem | Memvalidasi kesesuaian konfirmasi password |
| 9 | Sistem | **[Tidak cocok]** Menampilkan pesan error → kembali ke langkah 2 |
| 10 | Sistem | Mengecek duplikasi username di database |
| 11 | Sistem | **[Sudah ada]** Menampilkan pesan "Username sudah digunakan" → kembali ke langkah 2 |
| 12 | Sistem | Meng-hash password dengan PBKDF2 (Werkzeug) |
| 13 | Sistem | Menyimpan AppUser baru ke database |
| 14 | Sistem | Redirect ke halaman Login |

**Decision points:** 4 (validasi panjang username, panjang password, kecocokan password, duplikasi username)

---

## AD-02 · Login ★★ ✅

**Swimlanes:** EPC | Sistem
**TikZ file:** `bab3.tex` lines ~1048–1165; mirror at `.context/Diagram/activity-login.tex`
**label:** `fig:ad02-login`

| Step | Lane | Action |
|------|------|--------|
| 1 | EPC | Mengakses halaman Login |
| 2 | EPC | Mengisi form (username, password) |
| 3 | EPC | Menekan tombol Login |
| 4 | Sistem | Normalisasi username (lowercase, strip whitespace) |
| 5 | Sistem | Mencari user di database berdasarkan username |
| 6 | Sistem | **[User tidak ditemukan]** Menampilkan pesan error → kembali ke langkah 3 |
| 7 | Sistem | Memverifikasi password hash dengan Werkzeug check_password_hash |
| 8 | Sistem | **[Password salah]** Menampilkan pesan error → kembali ke langkah 3 |
| 9 | Sistem | Menyimpan user_id ke session |
| 10 | Sistem | Redirect ke halaman Dashboard |

**Decision points:** 2 (user ditemukan, password cocok)

**TikZ condensations vs description:**
- Steps 4+5 merged into one node: "Normalisasi username;\\ cari user di database"
- Both F-branch error nodes labeled "Tampilkan\\ pesan error" (same text, different loop targets)
- Loop-back for both errors targets `click` node (step 3), not step 2 — click is the trigger in the diagram
- Step 10 simplified: `_safe_next()` detail omitted; rendered as "Redirect ke halaman Dashboard"

---

## AD-03 · Import Data Mahasiswa ★★★ ✅

**TikZ file:** `.context/Diagram/activity-import.tex` · **label:** `fig:ad03-import`

**Swimlanes:** EPC | Sistem

| Step | Lane | Action |
|------|------|--------|
| 1 | EPC | Membuka halaman Data Center (`/data`) |
| 2 | EPC | **[Pilih sumber]** Gunakan file default di server ATAU upload file baru |
| — | — | **Cabang A: File Default** |
| 3a | EPC | Menekan tombol "Import Default" |
| 4a | Sistem | Membaca path dari env `DEFAULT_EXCEL_PATH` |
| — | — | **Cabang B: Upload File** |
| 3b | EPC | Memilih file Excel dan menekan tombol Upload |
| 4b | Sistem | Menerima file bytes dari HTTP multipart |
| — | — | **Bergabung kembali** |
| 5 | Sistem | Membaca sheet Excel dengan pandas (`ExcelIO`) |
| 6 | Sistem | Memvalidasi keberadaan 6 kolom wajib (STUDENT ID, TRACK, PARTNER/LECTURER, POSITION/TOPIC, WORK SCHEMA, GPA) |
| 7 | Sistem | **[Kolom tidak lengkap]** Mengembalikan error ke EPC → selesai |
| 8 | Sistem | Iterasi per baris: membersihkan data (`_clean_id`, `_clean_text`, `_clean_float`, dll.) |
| 9 | Sistem | Untuk setiap mahasiswa: cek apakah student_id sudah ada di database |
| 10 | Sistem | **[Ada]** Update data mahasiswa (upsert) |
| 11 | Sistem | **[Belum ada]** Insert mahasiswa baru |
| 12 | Sistem | Menampilkan ringkasan hasil import (total baris, inserted, updated, error) |

**Decision points:** 3 (sumber file, validasi kolom, upsert check)
**Key complexity:** upsert loop per row, dual import path

---

## AD-04 · Kelola Data Faculty Supervisor ★★ ✅

**TikZ file:** `.context/Diagram/activity-supervisor.tex` · **label:** `fig:ad04-supervisor`

**Swimlanes:** EPC | Sistem

| Step | Lane | Action |
|------|------|--------|
| 1 | EPC | Membuka halaman Supervisors (`/supervisors`) |
| 2 | Sistem | Menampilkan daftar semua supervisor dengan status aktif/nonaktif |
| 3 | EPC | **[Pilih aksi]** Tambah supervisor baru ATAU lihat detail/edit supervisor |
| — | — | **Cabang A: Tambah Supervisor** |
| 4a | EPC | Mengisi form (kode, nama, keywords awal) dan submit |
| 5a | Sistem | Mengecek apakah kode supervisor sudah ada |
| 6a | Sistem | **[Kode ada]** Update nama dan keywords |
| 7a | Sistem | **[Kode baru]** Insert supervisor baru dengan is_active=True |
| 8a | Sistem | Refresh daftar supervisor |
| — | — | **Cabang B: Export Konfigurasi** |
| 4b | EPC | Menekan tombol Export |
| 5b | Sistem | Generate file Excel (`supervisor_config_export.xlsx`, 2 sheet) |
| 6b | Sistem | Mengirim file sebagai download |

**Decision points:** 2 (pilih aksi, kode sudah ada)

---

## AD-05 · Kelola Keywords Supervisor ★★ ✅

**TikZ file:** `.context/Diagram/activity-keywords.tex` · **label:** `fig:ad05-keywords`

**Swimlanes:** EPC | Sistem

| Step | Lane | Action |
|------|------|--------|
| 1 | EPC | Memilih supervisor dari dropdown di Keywords Studio |
| 2 | Sistem | Menampilkan chip keywords yang sudah ada untuk supervisor tersebut |
| 3 | EPC | Mengetik keyword baru di input field |
| 4 | EPC | Menekan Enter / Comma / tombol "Add" |
| 5 | Sistem | Normalisasi token (trim, collapse whitespace) |
| 6 | Sistem | **[Duplikat (case-insensitive)]** Abaikan token, kembali ke langkah 3 |
| 7 | Sistem | Menambahkan chip keyword baru ke tampilan |
| 8 | EPC | (Opsional) Menghapus chip keyword yang tidak relevan |
| 9 | EPC | Menekan tombol Save |
| 10 | Sistem | Menggabungkan semua keyword aktif menjadi string comma-separated |
| 11 | Sistem | Update kolom `profile_keywords` di tabel `supervisors` |
| 12 | Sistem | Menampilkan konfirmasi berhasil |

**Decision points:** 1 (duplikasi keyword)
**Note:** Adaptive enrichment (auto-learn dari histori mahasiswa) terjadi di pipeline, bukan di sini.

---

## AD-06 · Lihat Dashboard ★

**Swimlanes:** EPC | Sistem

| Step | Lane | Action |
|------|------|--------|
| 1 | EPC | Mengakses `/dashboard` |
| 2 | Sistem | Mengambil data agregat: total mahasiswa, supervisor aktif, total run |
| 3 | Sistem | Mengambil run terbaru dan metric evaluasinya (match rate, MRR) |
| 4 | Sistem | Mengambil data 12 run terakhir untuk grafik tren |
| 5 | Sistem | Merender halaman: 4 KPI card + bar chart tren + tabel run terakhir |
| 6 | EPC | Membaca ringkasan dan memilih aksi lanjutan (klik View, Generate, dll.) |

**Decision points:** 0 (read-only display)
**Note:** Cukup sederhana — opsional dimasukkan ke thesis.

---

## AD-07 · Trigger Proses Rekomendasi ★★★ ✅

**TikZ file:** `.context/Diagram/activity-trigger.tex` · **label:** `fig:ad07-trigger`

**Swimlanes:** EPC | Sistem

| Step | Lane | Action |
|------|------|--------|
| 1 | EPC | Membuka modal konfigurasi run (dari Dashboard atau Data Center) |
| 2 | EPC | Mengatur parameter: embedding model, group bonus toggle, extra docs toggle, kapasitas min/max |
| 3 | EPC | Menekan tombol "Generate" |
| 4 | Sistem | Membuat objek `RunOverrides` dari parameter |
| 5 | Sistem | Memuat semua data mahasiswa dari database |
| 6 | Sistem | **[Tidak ada mahasiswa]** Mengembalikan error → selesai |
| 7 | Sistem | Memuat profil supervisor aktif; memperkaya secara adaptif dari histori assignment |
| 8 | Sistem | Membangun dokumen teks per mahasiswa (`student_document`) dan per supervisor (`profile_document`) |
| 9 | Sistem | Menginisialisasi EmbeddingProvider (lazy load model) |
| 10 | Sistem | **[SentenceTransformer tersedia]** Menghitung embedding → cosine similarity matrix |
| 11 | Sistem | **[SentenceTransformer gagal → TF-IDF]** Menghitung TF-IDF similarity matrix |
| 12 | Sistem | **[TF-IDF gagal → Token Overlap]** Menghitung Jaccard similarity matrix |
| 13 | Sistem | **[Group bonus aktif]** Menerapkan Company Group Bonus (logarithmic decay) ke score matrix |
| 14 | Sistem | Membuat capacity plan (`_build_capacity_plan`): hitung min/max per supervisor |
| 15 | Sistem | Menjalankan Greedy Assignment Solver (Phase 1: reduce overfull, Phase 2: fill underfull) |
| 16 | Sistem | Mengevaluasi hasil: MRR, Hit@1, Hit@5, NDCG@5, NDCG@10, Precision@5, Match Rate |
| 17 | Sistem | Membangun rankings JSON (top-5 kandidat per mahasiswa) |
| 18 | Sistem | Menyimpan `RecommendationRun` + N baris `Recommendation` ke database |
| 19 | Sistem | Redirect ke halaman Run Detail (`/runs/<id>`) |

**Decision points:** 4 (ada mahasiswa, backend embedding fallback ×2, group bonus aktif)
**Key complexity:** 3-tier embedding fallback + capacity-constrained greedy solver
**Most important diagram for thesis — represents the core algorithm pipeline.**

---

## AD-08 · Lihat Riwayat Run ★

**Swimlanes:** EPC | Sistem

| Step | Lane | Action |
|------|------|--------|
| 1 | EPC | Mengakses halaman `/runs` |
| 2 | Sistem | Mengambil daftar hingga 250 run terbaru dari database |
| 3 | Sistem | Menampilkan tabel: run ID, tanggal, jumlah mahasiswa, objective score, match rate |
| 4 | EPC | Memilih run untuk dilihat detailnya |
| 5 | EPC | Redirect ke `/runs/<id>` (→ AD-09) |

**Decision points:** 0
**Note:** Alur sederhana, tidak perlu diagram terpisah — cukup disebutkan dalam narasi.

---

## AD-09 · Lihat Hasil Rekomendasi ★★★ ✅

**TikZ file:** `.context/Diagram/activity-hasil.tex` · **label:** `fig:ad09-hasil`

**Swimlanes:** EPC | Sistem

| Step | Lane | Action |
|------|------|--------|
| 1 | EPC | Membuka `/runs/<id>` (Run Detail) |
| 2 | Sistem | Mengambil `RecommendationRun` berdasarkan id |
| 3 | Sistem | **[Run tidak ditemukan]** Mengembalikan 404 → selesai |
| 4 | Sistem | Menampilkan: metric evaluasi, kapasitas per supervisor, mismatch spotlight (top 10) |
| 5 | EPC | Berpindah ke tab Rekomendasi (`/runs/<id>/recommendations`) |
| 6 | EPC | (Opsional) Mengisi filter: teks pencarian, dropdown supervisor, toggle mismatch-only |
| 7 | Sistem | Menjalankan query server-side dengan filter yang aktif |
| 8 | Sistem | Menampilkan tabel rekomendasi (Student ID, Nama, Track, Supervisor Saat Ini, Supervisor Rekomendasi, Final Score) |
| 9 | Sistem | Menyorot baris mismatch dengan CSS class `.bad` |
| 10 | EPC | **[Ingin export]** Lanjut ke AD-10 |
| 11 | EPC | **[Selesai]** Kembali ke daftar run |

**Decision points:** 2 (run ditemukan, apply filter)

---

## AD-10 · Export Hasil ke Excel ★★ ✅

**TikZ file:** `.context/Diagram/activity-export.tex` · **label:** `fig:ad10-export`

**Swimlanes:** EPC | Sistem

| Step | Lane | Action |
|------|------|--------|
| 1 | EPC | Dari halaman Run Detail, memilih tipe export: Standard atau Detailed |
| — | — | **Cabang A: Standard Export** |
| 2a | Sistem | Mengambil data rekomendasi, ringkasan kapasitas, evaluasi dari database |
| 3a | Sistem | Membuat workbook Excel (openpyxl): sheet `recommendations`, `summary`, `evaluation` |
| 4a | Sistem | Mengirim file `rekomendasi_dosen_run_{id}.xlsx` sebagai HTTP attachment |
| — | — | **Cabang B: Detailed Export** |
| 2b | Sistem | Mengambil data di atas + rankings JSON |
| 3b | Sistem | Membuat workbook Excel: 4 sheet (tambah sheet `rankings`) |
| 4b | Sistem | Mengirim file `rekomendasi_dosen_run_{id}_detailed.xlsx` sebagai HTTP attachment |
| — | — | **Bergabung** |
| 5 | EPC | File terunduh otomatis ke perangkat |

**Decision points:** 1 (jenis export)

---

## AD-11 · Logout ★

**Swimlanes:** EPC | Sistem

| Step | Lane | Action |
|------|------|--------|
| 1 | EPC | Menekan tombol Logout |
| 2 | Sistem | Menerima POST request ke `/logout` |
| 3 | Sistem | Menghapus `session["user_id"]` |
| 4 | Sistem | Redirect ke halaman Login |

**Decision points:** 0
**Note:** Tidak perlu diagram terpisah.

---

## Prioritization Summary for Thesis (BAB 3)

| AD | Use Case | Priority | Status | File |
|----|----------|----------|--------|------|
| AD-07 | Trigger Proses Rekomendasi | ★★★ | ✅ | `activity-trigger.tex` · `fig:ad07-trigger` |
| AD-03 | Import Data Mahasiswa | ★★★ | ✅ | `activity-import.tex` · `fig:ad03-import` |
| AD-09 | Lihat Hasil Rekomendasi | ★★★ | ✅ | `activity-hasil.tex` · `fig:ad09-hasil` |
| AD-01 | Register | ★★ | ✅ | `activity-register.tex` · `fig:ad01-register` |
| AD-02 | Login | ★★ | ✅ | inline `bab3.tex` ~1048–1165 · `fig:ad02-login` |
| AD-04 | Kelola Data Faculty Supervisor | ★★ | ✅ | `activity-supervisor.tex` · `fig:ad04-supervisor` |
| AD-05 | Kelola Keywords Supervisor | ★★ | ✅ | `activity-keywords.tex` · `fig:ad05-keywords` |
| AD-10 | Export Hasil ke Excel | ★★ | ✅ | `activity-export.tex` · `fig:ad10-export` |
| AD-06 | Lihat Dashboard | ★ | — | Read-only, no branching — omit or prose |
| AD-08 | Lihat Riwayat Run | ★ | — | No branching, simple list — omit or prose |
| AD-11 | Logout | ★ | — | Trivial — omit or prose |

All ★★/★★★ diagrams inserted into `bab3.tex` via `\input{}` after AD-02 Login (lines ~1168–1187).
Insert order: AD-01, AD-03, AD-04, AD-05, AD-07, AD-09, AD-10 (each preceded by `\clearpage`).