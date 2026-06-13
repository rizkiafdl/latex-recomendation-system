# Use Case Diagram Description — Sistem Rekomendasi Faculty Supervisor

Revised 2026-06-13. Source file: `.context/Diagram/use-case.tex`

---

## What Changed from Previous Version

| Previous | Revised | Reason |
|---|---|---|
| "Kelola Rules Studio" | "Kelola Keywords Supervisor" | "Rules Studio" does not exist; actual feature is Keywords Studio (`/supervisors/keywords/update`) |
| 10 use cases | 11 use cases | Added "Lihat Riwayat Run" (`/runs` route) |
| Description says "user dan admin" | Description says single actor "EPC" | No admin role in codebase — `app_users` table is flat, all users have identical permissions via `@login_required` |
| "sistem klasifikasi dan prediksi topik" in description | "Sistem Rekomendasi Faculty Supervisor" | Stale boilerplate from an older version |

---

## Actor

### EPC (single actor)
- Represents the study program coordinator who operates the system end-to-end.
- There is **no role-based access control** in the system. All authenticated users share the same capabilities.
- Authentication is session-based (`session["user_id"]`), enforced via `@login_required` on all protected routes.

---

## Use Cases

### 1. Register (`/register`)
- Create a new user account (username min 3 chars, password min 6 chars).
- Duplicate username check performed by `register_user()` in `services.py`.

### 2. Login (`/login`)
- Authenticate with username + password via Werkzeug PBKDF2 hash verification.
- Safe redirect via `_safe_next()` prevents open redirect vulnerability.

### 3. Import Data Mahasiswa (`/data/import-default`, `/data/import-upload`)
- Two sub-flows: import from the default Excel path on server, or upload a new file.
- Handled by `ExcelIO` module (`excel_io.py`); upsert semantics (update or insert by `student_id`).
- Required columns: STUDENT ID, TRACK, PARTNER/LECTURER, POSITION/TOPIC, WORK SCHEMA, GPA.

### 4. Kelola Data Faculty Supervisor (`/supervisors`, `/supervisors/add`)
- View supervisor roster with active status badges.
- Add or update supervisor (code, name, keywords) via `add_or_update_supervisor()`.
- Export supervisor config to Excel via `/supervisors/export`.

### 5. Kelola Keywords Supervisor (`/supervisors/keywords/update`)
- Keywords Studio UI: chip-based keyword management per supervisor.
- Keyword normalization (trim, collapse whitespace, case-insensitive dedup).
- Stored as comma-separated string in `supervisors.profile_keywords`.
- Adaptive enrichment: system auto-learns keywords from past student assignments.

### 6. Lihat Dashboard (`/dashboard`)
- 4 KPI cards: Total Students, Active Supervisors, Total Runs, Latest Match Rate.
- Horizontal bar chart: Match Rate + Hybrid MRR + Content MRR trend across recent runs.
- Latest run snapshot with configuration badges and action buttons.

### 7. Trigger Proses Rekomendasi (`/generate`)
- Execute the full 10-stage recommendation pipeline.
- Configurable via `RunOverrides`: embedding model, group bonus toggle, extra docs toggle, capacity targets.
- Stores result as a `RecommendationRun` + `Recommendation[]` records in DB.

### 8. Lihat Riwayat Run (`/runs`)
- Browse all past recommendation runs (up to 250 entries).
- Table shows: run ID, date, student count, supervisor count, objective score, match rate.

### 9. Lihat Hasil Rekomendasi (`/runs/<id>`, `/runs/<id>/recommendations`)
- Run detail: evaluation metrics (MRR, NDCG, Hit@K), capacity table, mismatch spotlight.
- Recommendation table: per-student assignment with final score, rule matches, similarity score.
- Server-side filtering: text search, supervisor dropdown, mismatch-only toggle.

### 10. Export Hasil ke Excel (`/runs/<id>/export`, `/runs/<id>/export/detailed`)
- Standard export: 3 sheets (recommendations, summary, evaluation).
- Detailed export: adds `rankings` sheet with top-N candidates per student.

### 11. Logout (`/logout`)
- POST request; clears `session["user_id"]`.

---

## Layout Notes (TikZ)

- 11 use cases, y-spacing = 1.5 units, range: y = 0 to y = -15.0
- Actor centred at (x = -0.8, y = -7.5) — vertical centre of the use case list
- System boundary: (1.0, 1.0) to (10.5, -16.2)
- All association lines fan from actor right arm-tip at (-0.28, -6.85)
- Figure uses `\scalebox{0.65}` for A4 fit; adjust if layout shifts on compile