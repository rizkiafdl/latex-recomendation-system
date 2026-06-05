# Project Overview — Skripsi BINUS

## What this is
Indonesian undergraduate thesis (Skripsi) for BINUS University, written in LaTeX.
Topic: recommendation system (likely student-supervisor matching based on file names and content).

## Document structure
- `Skripsi.tex` — main entry point, orchestrates all includes
- `Awal_konfigurasi.tex` — variables: author name, NIM, title, supervisors, etc. Edit this first.
- `bab1.tex` – `bab5.tex` — chapters (bab = chapter in Indonesian)
  - bab1: Pendahuluan (Introduction) — **migrated from docx 2025-12-20; 150 lines**
  - bab2: Tinjauan Pustaka (Literature Review) — **migrated from docx 2025-12-20; 417 lines, 1 longtable**
  - bab3: Metode Penelitian (Research Method) — **migrated from docx 2025-12-20; 984 lines, 26 use-case longtables, 4 figures**
  - bab4: Hasil dan Pembahasan (Results & Discussion) — hand-written, 209 lines, UI screenshots
  - bab5: Simpulan dan Saran (Conclusion & Recommendations) — **lipsum stub, not written yet**
- Front matter: `sampul.tex`, `sampul2.tex`, `abstrak.tex`, `abstract.tex`, `pengantar.tex`, `pengesahan.tex`, `pernyataan.tex`, `orisinalitas.tex`
- `ref.bib` — bibliography (BibTeX format, apacite style)
- `ta.sty` — custom BINUS thesis style (sets margins, fonts, header/footer, float placement)
- `pic/` — images: login, register, dashboard, datacenter, runhistory, rundetails, supervisorstudio, rulestudio, logo, kerangka-berpikir, usecase-diagram, image9 (class diagram), image10 (ERD)
- `.context/raw/Formated Version 20251220.docx` — source Word doc used for the migration
- `raw_output.tex` — intermediate pandoc output (gitignored or can be deleted; not part of the thesis)

## Key style decisions in ta.sty
- `\floatplacement{figure}{H}` — all figures placed at exact position (no floating)
- `headheight=0pt` — triggers fancyhdr warnings (known, harmless)
- `\usepackage[ConnyRevised]{fncychap}` — custom chapter heading style
- `\bibliographystyle{apacite}` — APA citation style

## GitHub repo
`git@github.com:rizkiafdl/latex-recomendation-system.git`

## Build output
`build/Skripsi.pdf` — tracked in git (excluded from gitignore via `!build/Skripsi.pdf`)
