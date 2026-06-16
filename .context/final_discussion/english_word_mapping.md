# English Word Italic Audit — Full Thesis

**Crawled:** 2026-06-16 | **Files:** bab1–bab5, abstrak, pengantar, abstract  
**Rule:** Per BINUS writing guide, every foreign (English) word in Indonesian prose must be wrapped in `\textit{}`. Words already in `\textit{}`, `\emph{}`, `\textbf{}`, `\texttt{}`, citation commands, acronyms (ALL CAPS), and proper nouns (XYZ University, EPC, Faculty Supervisor as institutional title) are **exempt**.

> **Abstract exception (confirmed from BINUS example, 2026-06-16):** The BINUS abstract format requires the **entire body** of both ABSTRACT and ABSTRAK sections to be in italic — not individual word italic. This means `abstrak.tex` body foreign words are resolved by wrapping the whole body in `\textit{…}`, not word-by-word. See Section 5 for the `abstrak.tex` format fix.

---

## 1. Cross-Chapter Recurring Terms (Fix Globally First)

These appear bare across **multiple chapters** — highest ROI to fix with a global search-replace.

| Term | Chapters | Total est. occurrences |
|------|----------|------------------------|
| `embedding` | bab1, bab2, bab3, bab4 | 30+ |
| `ground truth` | bab1, bab2, bab3, bab5 | 12+ |
| `semantic similarity` | bab1, bab2, bab3, bab4 | 10+ |
| `cosine similarity` | bab1, bab2, bab3 | 10+ |
| `decision support system` | bab1, bab2, bab3 | 6+ |
| `deployment` | bab2, bab3 | 8+ |
| `backend` | bab1, bab2, bab3, bab4 | 6+ |
| `machine learning` | bab1, bab2 | 6+ |
| `deep learning` | bab2 | 5+ |
| `batch` | bab1, bab2, bab4 | 6+ |
| `pipeline` | bab2, bab3, bab4 | 6+ |
| `use case` | bab3 | 7+ |
| `text embedding` | bab2, bab3 | 5+ |
| `scoring` | bab3 | 3+ |
| `website` | bab3 | 3+ |
| `input` | bab1, bab2, bab4, bab5 | 5+ |
| `noise` | bab2, bab3 | 4+ |
| `retrieval` | bab2, bab3 | 5+ |
| `natural language processing` | bab1 | 1+ |
| `greedy` / `greedy solver` | bab3 | 3+ |
| `token` | bab2, bab3, bab4 | 4+ |
| `cloud` | bab2, bab3 | 2+ |
| `container` | bab2, bab3 | 3+ |
| `database` | bab2, bab4 | 4+ |
| `keyword`/`keywords` | bab2, bab4 | 8+ |
| `import` | bab2, bab4 | 5+ |
| `information retrieval` | bab2, bab3 | 2+ |

---

## 2. Per-Chapter Breakdown

---

### BAB 1 (`bab1.tex`)

#### Technical Terms
| Bare term | Line(s) | Excerpt |
|-----------|---------|---------|
| `semantic similarity` | 23, 38, 54, 74, 113, 135 | `...sistem rekomendasi berbasis semantic similarity, yang memungkinkan...` |
| `machine learning` | 25 | `...pendekatan otomatis berbasis machine learning memiliki potensi...` |
| `cosine similarity` | 25 | `...pembobotan TF-IDF dan cosine similarity yang mencapai akurasi...` |
| `embedding` | 25 | `...berbasis semantic similarity menggunakan model embedding...` |
| `ground truth` | 27×2, 40, 56, 76, 99, 123 | `...hasil rekomendasi otomatis dengan ground truth...` |
| `decision support system` | 29, 115 | `...berperan sebagai decision support system yang memberikan rekomendasi...` |
| `backend` | 58, 119 | `...mencakup antarmuka pengguna dan backend...` |
| `stop words` | 109 | `...penghapusan kata-kata umum (stop words) yang tidak memiliki makna...` |
| `natural language processing` | 135 | `...semantic similarity, natural language processing, serta penelitian...` |
| `input` | 119 | `...alur kerja sistem rekomendasi mulai dari input data...` |

#### General English Words
| Bare term | Line(s) | Excerpt |
|-----------|---------|---------|
| `spreadsheet` | 11 | `...masih dilakukan secara manual menggunakan spreadsheet...` |
| `batch` | 11×2, 13 | `...satu hingga tiga minggu per batch dengan jumlah mahasiswa...` |

#### Phrases
| Bare term | Line | Note |
|-----------|------|------|
| `enrichment-nya` | 11 | Indonesianized suffix on English noun — should be `\textit{enrichment}-nya` |

---

### BAB 2 (`bab2.tex`)

> Highest total violations in the thesis. `embedding` alone appears 25+ times bare.

#### Technical Terms
| Bare term | Line(s) | Excerpt |
|-----------|---------|---------|
| `machine learning` | 21, 23, 23, 253 | `Sistem machine learning membangun model...` |
| `deep learning` | 27, 29, 29, 275, 277 | `Deep Learning merupakan bagian dari machine learning...` |
| `deep neural networks` | 27 | `...dengan banyak lapisan (deep neural networks) untuk mempelajari...` |
| `embedding` | 23, 43, 47, 51, 69, 93, 97, 99, 103, 107, 111, 113, 115, 127, 129, 137, 145, 149, 151, 157, 237, 241, 255, 279, 287, 299, 303, 311, 317 | `...melalui model embedding yang mempelajari...` |
| `text mining` | 39, 39, 57 | `Text mining merupakan proses ekstraksi...` |
| `text preprocessing` | 43, 45, 47 | `proses text preprocessing menggunakan pendekatan...` |
| `noise` | 43, 57 | `...untuk mengurangi noise dalam data teks...` |
| `input` | 43 | `...memastikan konsistensi format input yang akan diproses...` |
| `Case Folding` | 45 | `...terdiri dari tiga langkah, yaitu Case Folding...` |
| `Collapse Whitespace` | 45 | `...Non-Alfanumerik, Collapse Whitespace.` |
| `regular expression` | 47 | `...menggunakan regular expression untuk membersihkan teks...` |
| `stemming` | 51 | `...tahapan preprocessing tambahan seperti stemming...` |
| `stop word removal` | 51 | `...stemming dan stop word removal tidak diterapkan...` |
| `information retrieval` | 57 | `...fondasi utama dalam sistem information retrieval dan text mining...` |
| `baseline` | 69 | `TF-IDF digunakan sebagai baseline konseptual...` |
| `word embedding` | 93, 95, 97, 97, 99 | `Word embedding merupakan pendekatan...` |
| `subword` | 49, 95 | `...mekanisme tokenisasi subword (WordPiece/BPE)...` |
| `robust` | 95 | `...sehingga lebih robust terhadap variasi morfologi...` |
| `level` | 97, 299 | `...makna pada level kalimat atau dokumen...` |
| `transformer-based embedding` | 99, 107 | `...pemilihan transformer-based embedding yang digunakan...` |
| `self-attention` | 103 | `...menggunakan mekanisme self-attention untuk memodelkan...` |
| `bidirectional` | 111 | `...representasi teks dua arah (bidirectional)...` |
| `token` | 111, 151 | `...sehingga setiap token dipahami berdasarkan konteks...` |
| `dense retrieval` | 149 | `...mendukung tiga fungsi sekaligus: dense retrieval...` |
| `sparse retrieval` | 149 | `...dense retrieval, sparse retrieval, dan multi-vector retrieval...` |
| `multi-vector retrieval` | 149 | `...sparse retrieval, dan multi-vector retrieval...` |
| `benchmark` | 151 | `...performa kompetitif pada berbagai benchmark retrieval...` |
| `retrieval` | 145, 151, 163 | `...tugas semantic similarity dan retrieval dibandingkan...` |
| `semantic search` | 157 | `...dioptimalkan untuk tugas semantic search dan text matching...` |
| `text matching` | 157 | `...semantic search dan text matching...` |
| `trade-off` | 157 | `...untuk mengevaluasi trade-off antara ukuran model...` |
| `instruction tuning` | 163 | `...model E5 yang dilengkapi instruction tuning...` |
| `default` | 163 | `...digunakan sebagai model default karena...` |
| `ground truth` | 175, 241, 243 | `...item relevan (ground truth) berhasil muncul...` *(line 171 already \textit{} — these 3 are bare)* |
| `mapping` | 171, 241, 243 | `...ground truth (mapping dosen pembimbing aktual)...` |
| `top-K` | 175, 193, 207 | `...item relevan berada dalam top-K, dan 0...` |
| `Content-Based` | 171 | `...tiga tingkat skor, yaitu Content-Based, Hybrid Score...` |
| `Hybrid Score` | 171 | `...yaitu Content-Based, Hybrid Score, dan Assignment Match...` |
| `micro web framework` | 267 | `Flask merupakan micro web framework berbasis Python...` |
| `routing` | 267 | `...komponen inti seperti routing, templating...` |
| `templating` | 267 | `...seperti routing, templating, dan session management...` |
| `session management` | 267 | `...routing, templating, dan session management...` |
| `template engine` | 269 | `Jinja2 sebagai template engine untuk menghasilkan...` |
| `server-side rendering` | 269, 271 | `...HTML secara dinamis di sisi server (server-side rendering)...` |
| `request-response` | 271 | `...menangani seluruh alur request-response...` |
| `single-user` | 271 | `...skala sistem yang bersifat single-user...` |
| `neural network` | 275 | `...komputasi tensor dan pengembangan model neural network...` |
| `dynamic computation graph` | 277 | `...mendukung eksekusi dinamis (dynamic computation graph)...` |
| `embedded` | 283 | `...bersifat embedded dan file-based...` |
| `file-based` | 283 | `...bersifat embedded dan file-based...` |
| `client-server` | 283 | `...sistem database client-server seperti PostgreSQL...` |
| `overhead` | 285 | `...tanpa overhead konfigurasi server database...` |
| `single-process` | 287 | `...sebagai proses tunggal (single-process)...` |
| `vector store` | 287 | `...tidak memerlukan fitur vector store...` |
| `on-the-fly` | 287 | `...similarity dilakukan secara on-the-fly...` |
| `query` | 291, 295 | `...berinteraksi dengan database menggunakan objek Python tanpa menulis query SQL...` |
| `backend` | 255, 261, 311 | `...bahasa utama pada sisi backend untuk mengembangkan...` |
| `frontend` | 263 | `JavaScript digunakan pada sisi frontend...` |
| `client-side` | 259 | `...pada sisi klien (client-side)...` |
| `encoding` | 299, 303 | `...proses encoding teks dilakukan secara lokal...` |
| `siamese network` | 301 | `...menggunakan arsitektur siamese network...` |
| `cross-encoding` | 301 | `...melakukan cross-encoding untuk setiap pasangan...` |
| `batch` | 303 | `Proses encoding dilakukan secara batch...` |
| `dot product` | 139, 303 | `...hasil perkalian dot product antara vektor A dan B...` |
| `deployment` | 311, 317 | `...selama tahap pengujian dan deployment sistem...` |
| `cloud` | 315 | `...bukan pada layanan cloud publik...` |
| `container` | 317 | `...menggunakan Docker container...` |
| `web server` | 317 | `...termasuk web server, pipeline rekomendasi...` |
| `pipeline` | 317 | `...termasuk web server, pipeline rekomendasi...` |
| `software engineering` | 323 | `...berbasis objek dalam software engineering...` |
| `behavior` | 335 | `...mencakup perilaku (behavior) dan keadaan (state)...` |
| `state` | 335 | `...perilaku (behavior) dan keadaan (state)...` |
| `entities` / `entity` | 339×4 | `...dikelompokkan menjadi entities, yang direpresentasikan...` |
| `sentence embedding` | 379 | `...Penelitian yang menggunakan sentence embedding...` |
| `monolingual` | 379 | `...pada domain tertentu dan bersifat monolingual...` |

#### General English Words
| Bare term | Line | Excerpt |
|-----------|------|---------|
| `firewall` | 309 | `...layanan server yang berada di balik firewall...` |
| `browser` | 261 | `...mampu berjalan langsung di browser...` |
| `de facto` | 253 | `Python menjadi bahasa de facto...` |
| `import` | 271 | `...autentikasi pengguna, import data, pemrosesan rekomendasi...` |

---

### BAB 3 (`bab3.tex`)

#### Technical Terms
| Bare term | Line(s) | Excerpt |
|-----------|---------|---------|
| `information retrieval` | 66 | `...merupakan metode umum dalam information retrieval tradisional...` |
| `scoring` | 68, 1562, 1964 | `Pendekatan scoring yang digunakan menggabungkan...` |
| `text embedding` | 68, 175, 222, 269 | `...berbasis text embedding untuk mengukur kesesuaian...` |
| `company group bonus` | 68 | `...serta company group bonus untuk mempertimbangkan...` |
| `hybrid` | 72 | `...Berdasarkan skor hybrid yang dihasilkan...` |
| `greedy capacity-constrained solver` | 72 | `...menggunakan greedy capacity-constrained solver...` |
| `constraint` | 72 | `...yang telah memenuhi seluruh constraint kapasitas.` |
| `decision support system` | 88, 96, 167, 364 | `...dikembangkan sebagai decision support system...` |
| `use case` | 88, 364, 421×2, 423×2, 425 | `...termasuk UML dan use case, digunakan sebagai media...` |
| `use case diagram` | 421 | `...melengkapi use case diagram dengan mendeskripsikan...` |
| `use case narrative` | 423, 425 | `Perancangan use case narrative mengacu pada proses bisnis...` |
| `website` | 74, 167, 175 | `...implementasi sistem dalam bentuk aplikasi berbasis website.` |
| `fine-tuning` | 169 | `...tanpa melibatkan proses pelatihan atau fine-tuning model...` |
| `training` | 175 | `...tidak melibatkan proses pelatihan training dari awal...` |
| `open-source` | 175, 269 | `...memanfaatkan model text embedding open-source...` |
| `embedding` | 222, 269 | `...vektor embedding mahasiswa yang berada...` |
| `eligible` | 222 | `...atribut status eligible digunakan sebagai filter...` |
| `cosine similarity` | 269 | `...dihitung kemiripannya menggunakan cosine similarity.` |
| `multi-retrieval` | 291 | `...dengan arsitektur multi-retrieval...` |
| `top-5` | 293 | `...mengukur kualitas rekomendasi top-5...` |
| `retrieval` | 273 | `...dirancang untuk tugas representasi teks dan retrieval.` |
| `token` | 273 | `...mendukung panjang konteks hingga 32K token...` |
| `greedy solver` | 1562, 1964 | `...alokasi menggunakan greedy solver...` |
| `greedy` | 1964 | `...proses scoring, alokasi greedy, dan evaluasi...` |
| `waterfall` | 2106 | `...mengacu pada metodologi waterfall, di mana setiap tahap...` |
| `deployment` | 2108, 2162, 2164, 2170, 2188, 2208, 2254, 2274, 2297 | `...serta pengembangan dan deployment prototipe sistem...` |
| `backend` | 2162 | `...antarmuka pengguna dan backend untuk memproses data...` |
| `cloud` | 2164 | `...berbasis cloud dengan konfigurasi single-process...` |
| `single-process` | 2164 | `...dengan konfigurasi single-process...` |
| `reverse proxy` | 2168, 2267 | `...yang bertindak sebagai reverse proxy dengan dukungan SSL...` |
| `server` | 2168 | `...diteruskan ke server aplikasi gunicorn...` |
| `input` | 2144 | `...berupa vektor teks yang digunakan sebagai input utama...` |
| `ranking` | 2158 | `...ditampilkan dalam bentuk peringkat (ranking)...` |
| `worker process` | 2177 | `...menjalankan Flask dengan satu worker process...` |

#### General English Words (Infrastructure Block)
| Bare term | Line | Excerpt |
|-----------|------|---------|
| `swap file` | 2261 | `...konfigurasi swap file 4 GB...` |
| `firewall` | 2261 | `...konfigurasi firewall untuk membatasi akses...` |
| `image container` | 2213 | `...mendefinisikan image container berbasis python:3.11-slim...` |
| `runtime` | 2213 | `...diunduh sekali saat runtime dan disimpan...` |
| `service` | 2258 | `...docker-compose untuk menghapus service yang tidak digunakan...` |
| `container` | 2263 | `...mengelola container tanpa hak akses root.` |
| `timeout` | 2267 | `...dengan parameter timeout yang disesuaikan (360 detik)...` |
| `certbot` | 2267 | `...menggunakan certbot apabila nama domain tersedia.` |
| `seeding` | 2265 | `...serta inisialisasi basis data dan seeding data faculty supervisor.` |
| `End-to-End` | 2270 | `Verifikasi End-to-End: Pengujian fungsional sistem...` *(inside \textbf, still needs \textit)* |

#### NLP Preprocessing Terms (Lines 2125–2136)
| Bare term | Line | Excerpt |
|-----------|------|---------|
| `Data cleansing` | 2125 | `Data cleansing, yaitu penghapusan data yang tidak relevan...` |
| `Case folding` | 2127 | `Case folding, untuk mengubah seluruh teks menjadi huruf kecil...` |
| `stopwords` | 2131 | `Penghapusan stopwords, untuk menghilangkan kata-kata umum...` |
| `noise` | 2136 | `...bertujuan untuk mengurangi noise pada data...` |

---

### BAB 4 (`bab4.tex`)

#### Technical Terms (Body Prose)
| Bare term | Line(s) | Excerpt |
|-----------|---------|---------|
| `semantic similarity` | 10 | `...sistem rekomendasi Faculty Supervisor berbasis semantic similarity.` |
| `meranking` | 122 | `model tersebut meranking supervisor yang "paling tepat"...` ⚠️ *Indonesianized English verb — should be `memeringkat` or `\textit{ranking}`* |
| `slot` | 274 | `...konsekuensi matematis dari keterbatasan slot...` |
| `mapping` | 278 | `...melakukan pemetaan (mapping) antara mahasiswa...` |
| `dataset` | 325 | `...pusat pengelolaan data masukan (dataset)...` |
| `pipeline trigger` | 325, 495 | `...pengendali eksekusi awal (pipeline trigger) sistem.` |
| `pipeline` | 325, 495, 537 | `...interaksi pipeline trigger.` |
| `Extract, Transform, Load` | 325 | `...proses ETL (Extract, Transform, Load) guna...` |
| `database` | 337, 485, 549, 603 | `...hasil tersimpan di database.` |
| `batch` | 180 | `...perbandingan antar batch eksperimen...` |
| `semantic keywords` | 393 | `...mengelola repositori dosen pembimbing dan semantic keywords.` |
| `searchable dropdown` | 393 | `...halaman ini meliputi searchable dropdown...` |
| `chip` | 393, 601×2, 603 | `...komponen chip kata kunci secara...` |
| `rules boost` | 524 | `...memilih model dan rules boost yang akan diterapkan...` |
| `metadata` | 555 | `...kebenaran metadata yang ditampilkan serta fungsionalitas button.` |
| `server-side` | 615 | `...fitur filter server-side.` *(correctly italicized at line 381 — bare here)* |
| `Black Box` | 409 | `Pengujian Black Box dilakukan untuk memvalidasi...` |
| `Equivalence Partitioning` | 409 | `Metode yang digunakan adalah Equivalence Partitioning...` |
| `input` / `output` | 409 | `...memastikan bahwa setiap input memberikan output yang sesuai...` |

#### General English Words (UI elements, Tables + Body)
| Bare term | Line(s) | Excerpt |
|-----------|---------|---------|
| `interface` | 290 | `Antarmuka (interface) ini mencakup kolom...` |
| `field` / `fields` | 290, 302 | `...mencakup kolom (field) username...` |
| `username` | 290, 424, 426, 428, 435, 446, 450, 452 | `...kolom (field) username dan kata sandi...` |
| `password` | 302, 435, 446, 448, 454 | `...mengisi beberapa fields seperti nama lengkap, username, password...` |
| `message` / `error message` | 426, 428, 448, 450, 506, 508 | `Sistem menampilkan message "Kredential Salah"` |
| `import` | 495, 506, 508, 516 | `...memvalidasi fungsi manajemen import data...` |
| `export` | 483, 572 | `Sistem akan melakukan export pada data run terakhir` |
| `upload` | 506, 508, 510, 512, 516 | `Upload Data Kosong` |
| `button` | 555 | `...serta fungsionalitas button.` |
| `dropdown` | 593, 632 | `Membuka menu dropdown dan mencari berdasarkan nama...` |
| `keyword`/`keywords` | 393, 595, 599×2, 601, 603, 605, 609 | `...semantic keywords. Fitur utama halaman ini...` |
| `toggle` | 541, 543, 634 | `Toggle group bonus yang dipilih akan berubah warna` |
| `tooltip` | 487 | `Sistem menampikan tooltip detail nilai Match Rate...` |
| `snapshot` | 489 | `Sistem menampikan Informasi Snapshot` |
| `pop-up` | 545 | `Sistem akan menutup pop-up new run` |
| `token` | 609 | `Sistem mengabaikan token duplikat...` |

---

### BAB 5 (`bab5.tex`)

> Mostly well-italicized. One miss in body prose.

| Bare term | Line | Excerpt |
|-----------|------|---------|
| `input` | 35 | `...akan mengurangi potensi kesalahan input data...` |

---

### `abstrak.tex` — ABSTRAK section (Indonesian prose)

> **Revised verdict (2026-06-16):** Per the BINUS example abstract, the correct fix is **wrapping the entire body in `\textit{}`** — not individual word italics. This automatically italicizes all foreign terms in both the ABSTRACT (English) and ABSTRAK (Indonesian) bodies. Individual word audit below is **superseded** by Section 5.

Terms that were bare (now resolved by full-body italic):
`semantic similarity` (44, 48), `decision support system` (44), `natural language processing` (46), `company group bonus` (46), `greedy` (46), `ground truth` (46), `backend` (48), `company-aware scoring` (48)

Additionally bare (judgment calls, also resolved by full-body italic):
`Faculty Supervisor` ×6, `Program Enrichment`, `Enrichment Program Coordinator`

---

### `pengantar.tex` / `abstract.tex`
No violations. `pengantar.tex` is pure Indonesian proper nouns. `abstract.tex` is entirely in English (exempt by full-body italic once fix is applied).

---

## 3. Priority Matrix

### P1 — Fix Immediately (examiner will notice)
These appear in opening sentences or are core thesis terms:
1. `semantic similarity` — first sentence of bab4, repeated everywhere
2. `ground truth` — central evaluation term, bare 12+ times across bab1–bab5
3. `decision support system` — used to define the system type
4. `embedding` — 30+ bare occurrences across bab2 alone
5. `abstrak.tex` — wrap ABSTRACT + ABSTRAK bodies in `\textit{}`, fix labels (see Section 5)
6. `meranking` (bab4 line 122) — non-standard form; replace with `memeringkat` or `\textit{ranking}`

### P2 — High (technical terms in body paragraphs)
`cosine similarity`, `machine learning`, `deep learning`, `use case`, `deployment`, `backend`, `pipeline`, `natural language processing`, `text embedding`, `word embedding`, `batch`, `noise`, `stop words` / `stop word removal`, `stemming`, `information retrieval`

### P3 — Medium (single or table occurrences)
`username`, `password`, `upload`, `import`, `export`, `button`, `dropdown`, `toggle`, `keyword`, `chip`, `tooltip`, `snapshot`, `pop-up`, `message`, `error message`, `server-side`, `database`, `metadata`, `slot`, `rules boost`, `searchable dropdown`

### P4 — Low / Judgment Call
- `website` — widely adopted into Indonesian; borderline
- `de facto` — Latin phrase; some styles exempt it
- `performa` — fully absorbed as Indonesian loanword; likely exempt
- `Excel`, `Docker`, `Nginx` — brand names; generally exempt
- `GPU`, `CPU`, `CUDA`, `WSGI` — acronyms; exempt

---

## 4. Fix Strategy

**Global search-replace approach** (safest, least risk of missing occurrences):

```bash
# Example pattern to find bare occurrences
grep -n "embedding" bab2.tex | grep -v "\\\\textit{" | grep -v "\\\\texttt{" | grep -v "%"
```

**Recommended order:**
1. Fix `abstrak.tex` — full-body italic wrap + label fixes (see Section 5); highest visual impact
2. Fix `embedding` globally across bab1–bab4 (highest count)
3. Fix `ground truth` globally (critical evaluation term)
4. Fix `semantic similarity`, `cosine similarity`, `decision support system`
5. Fix `deployment` in bab3 (8+ occurrences in infrastructure section)
6. Fix `use case` block in bab3
7. Address bab4 UI terms (P3)

---

## 5. `abstrak.tex` Format Fix (BINUS Example–Confirmed)

Confirmed from BINUS official abstract example: **both body sections must be fully italic**, not individual-word italic. Section labels must be **bold only** (not italic).

### Current vs Required

| Element | Current | Required |
|---------|---------|----------|
| Line 29: ABSTRACT label | `\textbf{\textit{ABSTRACT}}` | `\textbf{ABSTRACT}` — remove italic from label |
| Line 41: ABSTRAK label | `\textbf{ABSTRAK}` | `\textbf{ABSTRAK}` ✓ already correct |
| Lines 32–36: ABSTRACT body | plain text | wrap in `\textit{…\par}` |
| Lines 44–48: ABSTRAK body | plain text | wrap in `\textit{…\par}` |
| Keywords lines (38, 51) | `\textit{…}` ✓ | already correct |

### Patch to apply in `abstrak.tex`

```latex
% Line 29 — remove \textit from label:
\textbf{ABSTRACT}

% Lines 32–37 — wrap ABSTRACT body:
\textit{The manual process of mapping students...
...processes in higher education institutions.}
\par

% Line 41 — already correct:
\textbf{ABSTRAK}

% Lines 44–49 — wrap ABSTRAK body:
\textit{Proses pemetaan mahasiswa ke Faculty Supervisor...
...proses administrasi akademik di perguruan tinggi.}
\par
```

> Once the body is wrapped in `\textit{}`, all English terms inside (semantic similarity, decision support system, backend, etc.) become italic automatically — no word-level `\textit{}` needed inside the abstract.
