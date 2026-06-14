# Use Case Narrative Revision — Audit & Corrected Content

Source: `bab3.tex` lines 483–991 (Tabel 3.6 – 3.15)
Cross-referenced against: `feature-system-en.md`, `ActivityDiagramDescription.md`
Date: 2026-06-13

---

## Summary of Issues

| Table | Use Case | Severity | Issue |
|---|---|---|---|
| 3.10 | Kelola Rules Studio | 🔴 CRITICAL | Entire table is hallucinated — "Rules Studio" does not exist in the codebase |
| 3.11 | Trigger Proses Rekomendasi | 🔴 CRITICAL | Related Use Case references non-existent "Kelola Rules Studio" |
| 3.15 | Lihat Dashboard | 🔴 CRITICAL | Flow references "status rule boost" — `enable_rule_boost` was removed from pipeline |
| — | Lihat Riwayat Run | 🔴 CRITICAL | Use case exists in system (`/runs` route) but has no narrative table |
| 3.6 | Register | 🟡 MODERATE | Post-condition says "EPC langsung login" — system redirects to Login, does NOT auto-login |
| 3.9 | Kelola Data Faculty Supervisor | 🟡 MODERATE | Exception "Kode dosen < 3 karakter" not verified in codebase; Brief Description overlaps with separate Keywords use case |
| 3.11 | Trigger Proses Rekomendasi | 🟡 MODERATE | Exception says "fallback ke TF-IDF" only — system has 3-tier chain (TF-IDF → Token Overlap) |
| 3.11 | Trigger Proses Rekomendasi | 🟡 MODERATE | Flow: group bonus step is conditional (`enable_group_bonus`), written as if always runs |
| 3.13 | Export Hasil ke Excel | 🟡 MODERATE | Only describes Standard Export (3 sheets); Detailed Export (4 sheets + rankings) missing |
| 3.13 | Export Hasil ke Excel | 🟡 MODERATE | Flow mentions "rule boost" as a data field — `rule_boost` removed from pipeline |
| 3.7 | Login | 🟢 MINOR | Flow missing: username normalization (lowercase) before lookup |
| 3.11 | Trigger Proses Rekomendasi | 🟢 MINOR | Flow missing: adaptive profile enrichment + rankings JSON build steps |
| 3.12 | Lihat Hasil Rekomendasi | 🟢 MINOR | Related Use Case missing: "Lihat Riwayat Run" |

**Table numbering after revision:** Adding "Kelola Keywords Supervisor" (replaces 3.10) and "Lihat Riwayat Run" (new table → 3.16, or reorder). Recommend inserting after Tabel 3.9 and pushing old 3.10 to 3.11, shifting all subsequent by +1, final table = 3.16.

Recommended final order:
- 3.6 Register, 3.7 Login, 3.8 Import Data, 3.9 Kelola Supervisor,
  **3.10 Kelola Keywords Supervisor** (REPLACE), 3.11 Lihat Dashboard,
  3.12 Trigger Proses Rekomendasi, **3.13 Lihat Riwayat Run** (NEW),
  3.14 Lihat Hasil Rekomendasi, 3.15 Export Hasil, 3.16 Logout

---

## 🔴 CRITICAL: Replace Tabel 3.10 — "Kelola Keywords Supervisor"

**Old content:** Everything in "Kelola Rules Studio" is fabricated. No route, no DB column, no service method matches this description. Remove entirely.

**Correct content (Tabel 3.10):**

| Elemen | Deskripsi |
|---|---|
| Use Case Name | Kelola Keywords Supervisor |
| Scenario | EPC mengelola keyword kompetensi masing-masing faculty supervisor melalui Keywords Studio |
| Triggering Events | EPC memilih supervisor dan membuka Keywords Studio di halaman Supervisors |
| Brief Description | Use case ini menjelaskan proses penambahan, pengeditan, dan penghapusan keyword kompetensi faculty supervisor menggunakan antarmuka chip berbasis token (Keywords Studio). Keyword yang tersimpan digunakan sebagai profil supervisor dalam pipeline rekomendasi. |
| Actor | EPC |
| Related Use Case | Kelola Data Faculty Supervisor, Trigger Proses Rekomendasi |
| Stakeholder | EPC, Faculty Supervisor |
| Pre-condition | EPC telah login; data faculty supervisor telah tersedia di sistem |
| Post-condition | Keyword supervisor tersimpan di kolom `profile_keywords` pada tabel `supervisors`; profil siap digunakan pada run rekomendasi berikutnya |

Flow Of Events:

| Actor | System |
|---|---|
| EPC membuka halaman Supervisors dan memilih supervisor dari dropdown | Sistem menampilkan chip keyword yang sudah tersimpan untuk supervisor tersebut |
| EPC mengetik keyword baru di input field | — |
| EPC menekan Enter, Comma, atau tombol Add | Sistem menormalisasi token (trim, collapse whitespace) dan melakukan pengecekan duplikasi (case-insensitive) |
| — | Jika bukan duplikat: Sistem menambahkan chip keyword baru ke tampilan |
| EPC menghapus chip keyword yang tidak relevan (klik ×) | Sistem menghapus keyword dari tampilan sementara |
| EPC menekan tombol Save | Sistem menggabungkan semua keyword aktif menjadi string comma-separated dan menyimpan ke kolom `profile_keywords` di database |
| — | Sistem menampilkan konfirmasi berhasil |

Exception Conditions:

| Condition | Handling |
|---|---|
| Keyword yang ditambahkan sudah ada (duplikat, case-insensitive) | Sistem mengabaikan token duplikat dan tidak menambahkan chip baru |
| Supervisor tidak ditemukan saat simpan | Sistem menampilkan pesan kesalahan |
| EPC belum login (session expired) | Sistem mengarahkan EPC ke halaman login secara otomatis |

---

## 🔴 CRITICAL: New Table — "Lihat Riwayat Run" (Tabel 3.13 baru)

This use case corresponds to route `/runs` — fully implemented, no narrative table exists yet.

| Elemen | Deskripsi |
|---|---|
| Use Case Name | Lihat Riwayat Run |
| Scenario | EPC menelusuri seluruh riwayat eksekusi proses rekomendasi yang pernah dilakukan |
| Triggering Events | EPC membuka menu Runs atau memilih tautan riwayat dari Dashboard |
| Brief Description | Use case ini menggambarkan proses penelusuran daftar run rekomendasi yang tersimpan di database, meliputi informasi ringkasan setiap run (waktu, jumlah mahasiswa, objective score, match rate) serta navigasi menuju detail run tertentu |
| Actor | EPC |
| Related Use Case | Lihat Hasil Rekomendasi, Trigger Proses Rekomendasi |
| Stakeholder | EPC, Program Studi |
| Pre-condition | EPC telah login ke sistem |
| Post-condition | EPC memperoleh daftar seluruh run dan dapat memilih run untuk dilihat detailnya |

Flow Of Events:

| Actor | System |
|---|---|
| EPC membuka halaman Riwayat Run (`/runs`) | Sistem mengambil hingga 250 run terbaru dari database, diurutkan berdasarkan waktu terbaru |
| — | Sistem menampilkan tabel riwayat run (Run ID, tanggal eksekusi, jumlah mahasiswa, jumlah supervisor, objective score, match rate, embedding model) |
| EPC memilih run yang ingin dilihat detailnya | Sistem mengarahkan ke halaman detail run (`/runs/<id>`) |

Exception Conditions:

| Condition | Handling |
|---|---|
| Belum ada run yang pernah dilakukan | Sistem menampilkan tabel kosong dengan informasi "Belum ada run tersedia" |
| EPC belum login (session expired) | Sistem mengarahkan EPC ke halaman login secara otomatis |

---

## 🔴 CRITICAL: Fix Tabel 3.15 — Lihat Dashboard (Flow line 5)

**Line to fix:** "Sistem mengekstrak konfigurasi pipeline dari run terakhir (model embedding, **status rule boost**, group bonus, extra docs)"

**Corrected:** "Sistem mengekstrak konfigurasi pipeline dari run terakhir (model embedding, group bonus, extra docs, similarity weight)"

**Reason:** `enable_rule_boost` was removed from `RunOverrides`. The actual pipeline config fields stored in `pipeline_config_json` are group bonus, extra docs, similarity weight, embedding model.

---

## 🟡 MODERATE: Fix Tabel 3.6 — Register (Post-condition)

**Current:** "Akun EPC tersimpan di database, EPC langsung login ke sistem"

**Corrected:** "Akun EPC tersimpan di database dengan password ter-hash (PBKDF2); EPC diarahkan ke halaman Login untuk melakukan autentikasi"

**Reason:** `register_user()` in `services.py` only creates the user record — it does not set `session["user_id"]`. After registration, the route redirects to `/login`.

---

## 🟡 MODERATE: Fix Tabel 3.9 — Kelola Data Faculty Supervisor

**Remove exception:** "Kode dosen kurang dari 3 karakter → Sistem menolak penyimpanan"
→ Not validated in the codebase. The `code` column is `String(16)` with only a `Unique` constraint.

**Fix Brief Description:** Remove "pembaruan keywords" from scope — keywords are managed in the separate "Kelola Keywords Supervisor" use case.

**Corrected Brief Description:** "Use case ini menjelaskan proses pengelolaan data profil faculty supervisor, termasuk penambahan supervisor baru, pembaruan nama dan status aktif, serta ekspor konfigurasi supervisor ke file Excel"

**Corrected Exception table:**

| Condition | Handling |
|---|---|
| Nama supervisor sudah digunakan oleh kode lain | Sistem menampilkan pesan konflik (UNIQUE constraint pada kolom name) |
| Kode supervisor atau nama kosong | Sistem menampilkan pesan validasi |
| EPC belum login (session expired) | Sistem mengarahkan EPC ke halaman login secara otomatis |

---

## 🟡 MODERATE: Fix Tabel 3.11 — Trigger Proses Rekomendasi

**Fix 1 — Related Use Case:**
- Current: "Import Data Mahasiswa, Kelola Data Faculty Supervisor, **Kelola Rules Studio**"
- Corrected: "Import Data Mahasiswa, Kelola Data Faculty Supervisor, **Kelola Keywords Supervisor**"

**Fix 2 — Flow (group bonus step):**
- Current: "Sistem menghitung company group bonus" (written as always executed)
- Corrected: "Jika parameter group bonus diaktifkan (enable\_group\_bonus = true), sistem menerapkan Company Group Bonus pada score matrix berdasarkan pengelompokan company partner mahasiswa"

**Fix 3 — Exception (embedding fallback):**
- Current: "Model embedding tidak tersedia → Sistem menggunakan fallback ke TF-IDF"
- Corrected: "Model SentenceTransformer tidak tersedia atau gagal dimuat → Sistem menggunakan fallback ke TF-IDF; jika TF-IDF juga gagal → Sistem menggunakan Token Overlap (Jaccard coefficient)"

**Fix 4 — Flow (add missing steps):**
After "Sistem membentuk dokumen teks mahasiswa dan faculty supervisor", add:
- "Sistem memperkaya profil supervisor secara adaptif dari histori assignment mahasiswa sebelumnya (adaptive profile enrichment)"

After "Sistem menghitung metrik evaluasi", add:
- "Sistem membangun rankings JSON (top-5 kandidat per mahasiswa beserta skor)"

---

## 🟡 MODERATE: Fix Tabel 3.13 — Export Hasil ke Excel

**Fix 1 — Brief Description:**
- Current: "Sistem menghasilkan file Excel berisi 3 sheet: recommendations, summary kapasitas, dan evaluation metrics"
- Corrected: "Sistem menghasilkan file Excel hasil rekomendasi dalam dua pilihan format: Standard Export (3 sheet: recommendations, summary, evaluation) atau Detailed Export (4 sheet: menambahkan sheet rankings berisi top-N kandidat per mahasiswa)"

**Fix 2 — Triggering Events:**
- Current: "EPC menekan tombol export pada halaman detail run"
- Corrected: "EPC menekan tombol Export Standard atau Export Detailed pada halaman detail run"

**Fix 3 — Flow (remove stale field "rule boost"):**
- Current: "skor similarity, **rule boost**, group boost, final score, rule matches"
- Corrected: "skor similarity, group boost, final score, rule matches, company group key"

**Fix 4 — Flow (add branching for two export types):**
Add as alternative path:
| Actor | System |
|---|---|
| EPC menekan tombol Export Detailed | Sistem mengambil data yang sama ditambah rankings JSON (top-N kandidat per mahasiswa) dan membentuk file Excel 4 sheet (recommendations, summary, evaluation, rankings) dengan nama `rekomendasi_dosen_run_{id}_detailed.xlsx` |

---

## 🟢 MINOR: Fix Tabel 3.7 — Login (Flow)

Add step after "EPC mengisi username":
- System: "Sistem menormalisasi username (lowercase, strip whitespace) sebelum melakukan pencarian di database"

---

## 🟢 MINOR: Fix Tabel 3.12 — Lihat Hasil Rekomendasi

**Add to Related Use Case:** "Trigger Proses Rekomendasi, Export Hasil ke Excel, **Lihat Riwayat Run**"

---

## Revised Table Order for bab3.tex

| Table No | Use Case | Status |
|---|---|---|
| 3.6 | Register | Minor fix (post-condition) |
| 3.7 | Login | Minor fix (normalization step) |
| 3.8 | Import Data Mahasiswa | OK |
| 3.9 | Kelola Data Faculty Supervisor | Moderate fix (remove unverified exception, narrow scope) |
| **3.10** | **Kelola Keywords Supervisor** | **REPLACE entirely** (was "Kelola Rules Studio") |
| 3.11 | Lihat Dashboard | Critical fix (remove "status rule boost") |
| 3.12 | Trigger Proses Rekomendasi | Moderate fix (3 changes) |
| **3.13** | **Lihat Riwayat Run** | **NEW TABLE** |
| 3.14 | Lihat Hasil Rekomendasi | Minor fix (related use case) |
| 3.15 | Export Hasil ke Excel | Moderate fix (add Detailed Export) |
| 3.16 | Logout | OK |

Note: old 3.11–3.15 shift to 3.12–3.16 after inserting the new table.
