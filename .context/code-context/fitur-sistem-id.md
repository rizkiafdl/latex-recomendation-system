# Konteks Kode: Sistem Rekomendasi Dosen Pembimbing (Bahasa Indonesia)

> Hasil crawl mendalam dari `/faculty-recommender-thesis` pada 2026-06-07.
> Tujuan: mendokumentasikan **semua fitur yang diimplementasi** untuk keperluan penulisan skripsi.

---

## 1. Gambaran Umum Sistem

Sistem ini adalah **aplikasi web berbasis Flask** yang merekomendasikan dosen pembimbing skripsi kepada mahasiswa menggunakan pendekatan **Content-Based Filtering** dengan embedding semantik. Pipeline terdiri dari 10 tahap mulai dari impor data Excel hingga penyimpanan hasil ke database dan ekspor laporan.

**Tech Stack:**
- Backend: Python 3.12, Flask 3.x
- ORM: SQLAlchemy 2.x (SQLite default)
- Embedding: SentenceTransformers (GPU/CPU) → TF-IDF → Token Overlap (rantai fallback)
- Frontend: Jinja2 + HTML/CSS (no framework), AJAX minimal
- Export: pandas + openpyxl

---

## 2. Pipeline Rekomendasi (10 Tahap)

### Tahap 1 — Impor Data Excel (`app/excel_io.py`)
- **Kolom wajib (6):** STUDENT ID, TRACK, PARTNER/LECTURER, POSITION/TOPIC, WORK SCHEMA, GPA
- **Kolom opsional (11):** BINUSIAN ID, STUDENT NAME, EMAIL, PHONE, TOTAL SKS, DURATION, JOB START/END DATE, ENROLLMENT STATUS, kode dosen, FS
- Normalisasi data: `_clean_id()`, `_clean_text()`, `_clean_float()`, `_clean_int()`, `_clean_date()` (tangani format Excel float `"1234.0"`)
- Mendukung impor dari path file lokal (`read_students_from_excel_path`) dan bytes upload (`read_students_from_excel_bytes`)
- Upsert ke DB: update jika student_id sudah ada, insert jika baru

### Tahap 2 — Pembangunan Dokumen (`app/rules.py`)
- **`normalize_text(value)`**: lowercase + hapus non-alphanumeric + collapse spasi → teks kanonikal
- **`student_document(student)`**: gabungkan track + position_topic + work_schema → teks dokumen mahasiswa
- **`profile_document(profile)`**: gabungkan keywords + labels supervisor → teks dokumen dosen

### Tahap 3 — Generasi Embedding & Similaritas (`app/embedding.py`)
**Rantai Fallback 3 Tingkat:**

1. **SentenceTransformer (primer):**
   - Model default: `intfloat/multilingual-e5-large-instruct`
   - Model alternatif: `BAAI/bge-m3`, `Qwen/Qwen3-Embedding-0.6B`
   - Encoding dengan L2 normalisasi → cosine similarity via dot product (matmul)
   - Resolusi device: `auto` → cek CUDA → CPU jika tidak tersedia
   - Thread-safe lazy loading (double-checked locking)
   - Singleton provider per model name (`_PROVIDERS` cache)

2. **TF-IDF (fallback 1):** scikit-learn `TfidfVectorizer(ngram_range=(1,2))` + cosine_similarity
3. **Token Overlap (fallback 2):** Jaccard coefficient antar set token

**Fungsi kunci:**
- `get_embedding_provider(model_name)`: ambil atau buat provider (singleton)
- `warmup_model(model_name)`: pre-load model di background thread
- `get_provider_statuses(model_names)`: status per model: `not_loaded`, `loading`, `ready`, `fallback`

### Tahap 4 — Skor Hybrid & Company Group Bonus (`app/recommender.py`)

**Score Matrix Assembly:**
```
weighted_similarity = similarity_matrix × SIMILARITY_WEIGHT   (default 1.0)
score_matrix = weighted_similarity + group_bonus (jika diaktifkan)
```

**Company Group Bonus (`_apply_company_group_bonus`):**
- Kelompokkan mahasiswa berdasarkan `partner_lecturer` yang dinormalisasi
- Syarat pemberian bonus:
  - Minimal 2 mahasiswa dari perusahaan yang sama
  - Diversitas topik ≤ 6 distinct topic token
  - Margin skor dosen terbaik vs. ke-2 ≥ 0.08
- Formula bonus: `effective_bonus = COMPANY_GROUP_BONUS / max(1.0, log2(group_size + 1))`
- Intuisi: kelompok besar mendapat bonus per-mahasiswa lebih kecil (diminishing returns)

### Tahap 5 — Perencanaan Kapasitas (`_build_capacity_plan`)
- Base: `target_min=10`, `target_max=12` mahasiswa per dosen
- **Overflow** (jumlah mahasiswa > total max): tambah slot ke `CAPACITY_PRIORITY_CODES` dahulu, lalu distribusi round-robin
- **Underflow** (jumlah mahasiswa < total min): kurangi min dari dosen prioritas terendah
- Safety: pastikan `sum(max) ≥ N`, `sum(min) ≤ N`, `min[j] ≤ max[j]`
- **4 Dosen Prioritas (`CAPACITY_PRIORITY_CODES`):**
  - D2211 (Dr. Abdul Haris Rangkuti), D6184 (Dr. Mochammad Haldi Widianto)
  - D6826 (Karen Etania Saputra), D1749 (Dr. Johan Muliadi Kerta)

### Tahap 6 — Solver Penugasan Greedy (`_solve_assignment`)
**Algoritma 2-Fase:**

1. **Init:** `assignment[i] = argmax(score_matrix[i, :])` — setiap mahasiswa ke dosen skor tertinggi
2. **Fase 1 — Kurangi Overfull:** pindahkan mahasiswa dari dosen kelebihan ke dosen yang masih ada kapasitas, pilih pemindahan dengan *penalty skor minimum*
3. **Fase 2 — Isi Underfull:** pindahkan dari dosen donor (count > min) ke dosen kekurangan, pilih *penalty skor minimum*

- Max iterasi: `N × M × 10`
- Objective = sum dari semua `score_matrix[i, assignment[i]]`
- Validasi akhir: error jika `min ≤ count ≤ max` tidak terpenuhi

### Tahap 7 — Evaluasi (`app/evaluation.py`)
**Tiga Sumber Evaluasi:**

| Pass | Matriks | Keterangan |
|------|---------|-----------|
| `content_based` | `content_similarity_matrix` | Hanya embedding semantik |
| `hybrid_score` | `hybrid_score_matrix` | Embedding + group bonus |
| `assignment_match` | Output solver | Akurasi penugasan akhir |

**Metrik Retrieval (dibandingkan `current_supervisor_code` sebagai ground truth):**

| Metrik | Formula | Arti |
|--------|---------|------|
| **MRR** | Σ(1/rank_i) / N | Rata-rata kebalikan posisi dosen benar |
| **Hit@1** | Count(rank≤1) / N | Dosen benar di posisi 1 |
| **Hit@5** | Count(rank≤5) / N | Dosen benar di top-5 |
| **NDCG@5** | Mean(1/log₂(rank+1)) jika rank≤5 | Kualitas ranking dengan diskon logaritmik |
| **NDCG@10** | Mean(1/log₂(rank+1)) jika rank≤10 | Sama, cutoff 10 |
| **Precision@5** | Count(rank≤5) / (N×5) | Presisi top-5 |
| **Avg Rank** | Mean(rank_i) | Posisi rata-rata dosen benar |
| **Match Rate** | matched / evaluated | Cocok dengan penugasan aktual saat ini |

- Mahasiswa tanpa `current_supervisor_code` dieksklusi dari evaluasi
- Semua nilai dibulatkan 6 desimal

### Tahap 8 — Penyimpanan DB (`app/services.py`)
`generate_and_store_recommendations()` menyimpan:
- Record `recommendation_runs`: input_source, total_students, total_supervisors, capacity info, solver info, embedding info, evaluation_json, pipeline_config_json, objective_score, rankings_json
- Record `recommendations` (N baris, satu per mahasiswa): run_id, student_id, supervisor_id, similarity_score, group_boost, final_score, rule_matches, company_group_key

### Tahap 9 — Rankings JSON (`_build_rankings_json`)
Per mahasiswa, simpan top-5 kandidat dosen:
```json
{
  "student_id": "...",
  "candidates": [
    {
      "rank": 1, "supervisor_code": "...", "similarity_score": 0.85,
      "group_boost": 0.05, "final_score": 0.90,
      "is_assigned": true, "score_delta_from_rank1": 0.0
    }
  ]
}
```
- Assigned supervisor selalu disertakan meski di luar top-5

### Tahap 10 — Ekspor Excel
**Ekspor Standar** (`rekomendasi_dosen_run_{id}.xlsx`, 3 sheet):
- `recommendations`: assignment lengkap dengan skor
- `summary`: kapasitas per dosen (assigned, min, max, within_capacity)
- `evaluation`: semua metrik evaluasi (section, metric, value)

**Ekspor Detailed** (`rekomendasi_dosen_run_{id}_detailed.xlsx`, 4 sheet):
- Menambahkan sheet `rankings`: semua kandidat per mahasiswa (top-N baris per mahasiswa)

**Ekspor Konfigurasi Supervisor** (`supervisor_config_export.xlsx`, 2 sheet):
- `supervisor_config`: daftar supervisor (id, code, name, keywords, is_active)
- `track_reference`: daftar track mahasiswa unik

---

## 3. Database Schema

### Tabel `supervisors`
| Kolom | Tipe | Keterangan |
|-------|------|-----------|
| id | Integer PK | - |
| code | String(16) Unique | Kode dosen (misal: D2211) |
| name | String(255) Unique | Nama lengkap |
| profile_keywords | Text | Kata kunci kompetensi |
| is_active | Boolean | Status aktif |

### Tabel `students`
| Kolom | Tipe | Keterangan |
|-------|------|-----------|
| student_id | String(32) Unique | NIM mahasiswa |
| binusian_id | String(32) | ID BINUS alternatif |
| name, email, phone | String | Data kontak |
| track | String(255) | Program studi |
| gpa | Float | IPK |
| total_sks | Integer | Total SKS |
| partner_lecturer | String(255) | Dosen mitra/perusahaan |
| position_topic | Text | Posisi & topik magang/skripsi |
| work_schema | String(64) | Skema kerja |
| current_supervisor_code | String(16) | Dosen pembimbing saat ini (ground truth) |

### Tabel `recommendation_runs`
- Menyimpan seluruh konfigurasi pipeline, metrik evaluasi, capacity bounds, rankings dalam format JSON
- Relasi One-to-Many ke `recommendations`

### Tabel `recommendations`
- Unique per (run_id, student_id)
- Menyimpan: similarity_score, group_boost, final_score, rule_matches, company_group_key

### Tabel `app_users`
- Authentication: username, password_hash (Werkzeug), full_name, created_at

---

## 4. Fitur Manajemen Supervisor (`/supervisors`)

### Keywords Studio (UI)
- Input berbasis datalist dengan autocomplete dari keyword yang sudah ada
- Penambahan token via tombol "Add" atau tekan Enter/Comma
- Normalisasi: trim + collapse whitespace, case-insensitive duplicate prevention
- Chip display dengan 6 warna bergilir (k0–k5)
- Penyimpanan: comma-separated ke `profile_keywords` di DB

### Adaptive Profile Enrichment (`_supervisor_profiles_from_db`)
- Sistem **otomatis belajar** dari riwayat penugasan mahasiswa:
  - Ambil mahasiswa yang pernah dibimbing dosen ini (lewat `current_supervisor_code`)
  - Ekstrak token dari dokumen mahasiswa, filter stopwords + token < 3 karakter
  - Ambil label dengan frekuensi ≥ 2 (top 8 label)
  - Gabungkan dengan keywords manual (top 16 token, frekuensi ≥ 2)
- Hasilnya: `SupervisorProfile` dengan keywords + labels yang diperkaya secara adaptif

---

## 5. Fitur Autentikasi

- Registrasi: validasi username (min 3 karakter), password (min 6 karakter), konfirmasi password, cek duplikat
- Login: normalisasi username (lowercase), verifikasi hash Werkzeug
- Session-based auth: `session["user_id"]`
- `@login_required` decorator pada semua route utama
- Redirect aman via `_safe_next()` (cegah open redirect)

---

## 6. Semua HTTP Route

| Route | Method | Fitur |
|-------|--------|-------|
| `/register`, `/login` | GET, POST | Autentikasi |
| `/logout` | POST | Hapus sesi |
| `/dashboard` | GET | KPI + trend chart + latest run snapshot |
| `/data` | GET | Health check, impor trigger |
| `/data/import-default` | POST | Impor dari file Excel default |
| `/data/import-upload` | POST | Upload & impor Excel baru |
| `/generate` | POST | Jalankan pipeline rekomendasi |
| `/runs` | GET | Riwayat semua run (max 250) |
| `/runs/<id>` | GET | Detail run: metrik, kapasitas, mismatch |
| `/runs/<id>/recommendations` | GET | Tabel rekomendasi + filter (q, supervisor, mismatch) |
| `/runs/<id>/export` | GET | Unduh Excel standar |
| `/runs/<id>/export/detailed` | GET | Unduh Excel dengan rankings |
| `/supervisors` | GET | Roster dosen + Keywords Studio |
| `/supervisors/add` | POST | Tambah/update dosen |
| `/supervisors/keywords/update` | POST | Simpan keywords dosen |
| `/supervisors/export` | GET | Ekspor konfigurasi supervisor |
| `/api/model-status` | GET | Status embedding models (JSON) |
| `/api/supervisors` | GET | Daftar supervisor aktif (JSON) |
| `/benchmark` | GET | Placeholder (belum diimplementasi) |

---

## 7. Konfigurasi Sistem (Variabel Environment)

| Variabel | Default | Keterangan |
|----------|---------|-----------|
| `DATABASE_URL` | `sqlite:///recommendation.db` | URL koneksi DB |
| `DEFAULT_EXCEL_PATH` | `map_2026.xlsx` | File input mahasiswa |
| `DEFAULT_SHEET_NAME` | `Sheet1` | Sheet Excel |
| `TARGET_MIN_CAPACITY` | `10` | Min mahasiswa per dosen |
| `TARGET_MAX_CAPACITY` | `12` | Max mahasiswa per dosen |
| `SIMILARITY_WEIGHT` | `1.0` | Bobot skor similarity |
| `COMPANY_GROUP_BONUS` | `0.2` | Bonus kelompok perusahaan |
| `HIGH_GPA_THRESHOLD` | `3.8` | Threshold IPK tinggi |
| `EMBEDDING_MODEL_NAME` | `intfloat/multilingual-e5-large-instruct` | Model embedding default |
| `EMBEDDING_DEVICE` | `auto` | Device (auto/cuda/cpu) |

**Model Embedding Tersedia:**
1. `BAAI/bge-m3` — BGE multilingual
2. `Qwen/Qwen3-Embedding-0.6B` — Qwen ringan
3. `intfloat/multilingual-e5-large-instruct` — E5 large multilingual **(DEFAULT)**

---

## 8. Threshold & Parameter Algoritma

| Parameter | Nilai | Lokasi | Makna |
|-----------|-------|--------|-------|
| Min group size (bonus) | 2 | `recommender.py` | Minimal 2 mahasiswa se-perusahaan untuk bonus |
| Max topic diversity | 6 | `recommender.py` | Skip bonus jika > 6 topik berbeda |
| Min margin supervisor | 0.08 | `recommender.py` | Skip bonus jika 2 dosen terbaik beda < 0.08 |
| Bonus decay | log₂(N+1) | `recommender.py` | Makin besar grup, bonus per mahasiswa makin kecil |
| Max solver iteration | N × M × 10 | `recommender.py` | Batas iterasi greedy per fase |
| Profile token threshold | ≥ 2 | `services.py` | Frekuensi minimal token untuk masuk profile dosen |
| Max learned keywords | 16 | `services.py` | Top keyword adaptif per supervisor |
| Max learned labels | 8 | `services.py` | Top label adaptif per supervisor |

---

## 9. Struktur File Proyek

```
faculty-recommender-thesis/
├── flask_app.py          # Entry point: routes + auth
├── seed.py               # CLI: init DB + seed data
├── requirements.txt      # Dependencies Python
├── Dockerfile / docker-compose.yml
├── app/
│   ├── config.py         # Semua konstanta & env vars
│   ├── models.py         # SQLAlchemy ORM models
│   ├── schemas.py        # Dataclasses: SupervisorProfile, RecommendationOutput, dll
│   ├── database.py       # Engine + SessionLocal
│   ├── queries.py        # Semua query ORM
│   ├── services.py       # Orkestrasi pipeline lengkap
│   ├── recommender.py    # Core: scoring + capacity + greedy solver
│   ├── embedding.py      # Multi-backend embedding provider
│   ├── rules.py          # Normalisasi teks + deteksi label + dokumen builder
│   ├── evaluation.py     # Metrik IR: MRR, NDCG, Hit@K, Precision, match rate
│   └── excel_io.py       # Import/export Excel
├── datasets/
│   ├── map_loader.py     # Loader Excel → extra_supervisor_docs
│   └── seed_dataset/     # Data seed: supervisor profiles, label_terms, stopwords
├── templates/            # Jinja2 HTML templates
│   ├── dashboard.html    # KPI + trend chart
│   ├── data_center.html  # Import + pipeline trigger
│   ├── supervisors.html  # Keywords Studio
│   ├── recommendations.html # Tabel hasil + filter
│   ├── runs.html         # Riwayat run
│   ├── run_detail.html   # Analisis run detail
│   └── benchmark.html    # Placeholder
└── static/style.css
```
