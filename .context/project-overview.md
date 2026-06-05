# Project Overview — Skripsi BINUS

## What this is
Indonesian undergraduate thesis (Skripsi) for BINUS University, written in LaTeX.
Topic: recommendation system (likely student-supervisor matching based on file names and content).

## Document structure
- `Skripsi.tex` — main entry point, orchestrates all includes
- `Awal_konfigurasi.tex` — variables: author name, NIM, title, supervisors, etc. Edit this first.
- `bab1.tex` – `bab5.tex` — chapters (bab = chapter in Indonesian)
  - bab1: Pendahuluan (Introduction)
  - bab2: Tinjauan Pustaka (Literature Review)
  - bab3: Metode Penelitian (Research Method)
  - bab4: Hasil dan Pembahasan (Results & Discussion) — most complete chapter
  - bab5: Simpulan dan Saran (Conclusion & Recommendations)
- Front matter: `sampul.tex`, `sampul2.tex`, `abstrak.tex`, `abstract.tex`, `pengantar.tex`, `pengesahan.tex`, `pernyataan.tex`, `orisinalitas.tex`
- `ref.bib` — bibliography (BibTeX format, apacite style)
- `ta.sty` — custom BINUS thesis style (sets margins, fonts, header/footer, float placement)
- `pic/` — images: login, register, dashboard, datacenter, runhistory, rundetails, supervisorstudio, rulestudio, logo

## Key style decisions in ta.sty
- `\floatplacement{figure}{H}` — all figures placed at exact position (no floating)
- `headheight=0pt` — triggers fancyhdr warnings (known, harmless)
- `\usepackage[ConnyRevised]{fncychap}` — custom chapter heading style
- `\bibliographystyle{apacite}` — APA citation style

## GitHub repo
`git@github.com:rizkiafdl/latex-recomendation-system.git`

## Build output
`build/Skripsi.pdf` — tracked in git (excluded from gitignore via `!build/Skripsi.pdf`)
