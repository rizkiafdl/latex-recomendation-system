# LaTeX Repo Schema Rules

Rules for any agent or contributor modifying this repo. Breaking these will cause the GitHub Actions CI to fail or produce a corrupted PDF.

---

## 1. Group balancing — CRITICAL

Every `\begingroup` MUST have a matching `\endgroup` in the same scope.

**Wrong (CI breaks):**
```latex
\begin{center}
    \begingroup
    \begin{tabular}{...}
    \end{tabular}
\end{center}   % this \endgroup closes \begingroup, not the center — center leaks!
```

**Correct:**
```latex
\begin{center}
    \begingroup
    \begin{tabular}{...}
    \end{tabular}
    \endgroup    % close explicit group BEFORE \end{center}
\end{center}
```

**Why this matters:** `\end{center}` internally calls `\endgroup`. If there's an unmatched `\begingroup` inside the center, `\end{center}` closes that instead of the center's own group. The center's group then leaks out and stays open until `\end{document}`, causing a fatal TeX error.

---

## 2. Centering images inside figures

Always use `\centering` (a declaration) inside `\begin{figure}`, never `\begin{center}...\end{center}`.

**Wrong:**
```latex
\begin{figure}
    \begin{center}
        \includegraphics[width=0.7\linewidth]{pic/image.png}
    \end{center}
\end{figure}
```

**Correct:**
```latex
\begin{figure}
    \centering
    \includegraphics[width=0.7\linewidth]{pic/image.png}
\end{figure}
```

**Why:** TeX Live 2026 (used by CI) loses track of `\begin{center}` inside `\begin{figure}[H]` (H placement from `float.sty`). `\centering` is a declaration with no open/close tracking and is the standard LaTeX idiom inside floats.

---

## 3. All environments must be balanced

For every `\begin{env}` there must be a matching `\end{env}` in the same file, at the same nesting level. This applies to: `center`, `table`, `tabular`, `figure`, `minipage`, `tabbing`, etc.

To verify a file is balanced before committing:
```bash
grep -c '\\begin{center}' file.tex   # must equal
grep -c '\\end{center}' file.tex
```

---

## 4. Adding new chapters or sections

New content files must be registered in `Skripsi.tex` using `\include{filename}` (without `.tex` extension). Do not use `\input` for chapter-level files — `\include` adds proper page breaks and aux file tracking.

```latex
% In Skripsi.tex, add your file in the correct position:
\include{bab6}   % example for a new chapter
```

---

## 5. Adding images

- Place image files in the `pic/` directory
- Supported formats: `.png`, `.jpg`, `.pdf`
- Reference without extension in LaTeX: `\includegraphics[...]{pic/filename}`
- Do NOT commit images to `build/` — that directory is for compiled output only

---

## 6. Bibliography citations

All `\cite{}` keys used in `.tex` files must exist in `ref.bib`. Using a key that doesn't exist produces `natbib Warning: Citation 'key' undefined` — this does not break the CI but produces a broken PDF with `[?]` placeholders.

BibTeX format used: `apacite` style. Entries must be valid BibTeX.

---

## 7. Labels must be unique

Every `\label{key}` must be unique across the entire document (all `.tex` files). Duplicate labels cause `LaTeX Warning: Label 'key' multiply defined` and cross-references become unreliable.

**Naming convention already in use:**
- Tables: `table:tab{chapter}{number}` e.g. `table:tab4.1`
- Equations: `equation-bab-{chapter}`
- Figures: follow existing patterns in `bab4.tex`

---

## 8. Do not modify `.latexmkrc`

The `.latexmkrc` is configured for both local (VS Code + LaTeX Workshop) and CI (GitHub Actions). Specifically:
- `$out_dir = 'build'` — output goes to `build/`, do not change
- `$pdf_mode = 1` — pdflatex mode, do not switch to xelatex/lualatex without updating CI workflow
- `$bibtex_use = 2` — bibtex runs automatically

---

## 9. Do not modify `.github/workflows/compile.yml` carelessly

Key settings that must stay:
- `args: -pdf -file-line-error -interaction=nonstopmode -f` — the `-f` flag is required for multi-pass compilation. Removing it causes CI to stop at first-pass errors.
- Do NOT add `-halt-on-error` — it kills pdflatex before latexmk can do its required second/third pass.
- `paths-ignore: ['build/Skripsi.pdf']` — prevents infinite loop when CI commits the PDF back.

---

## 10. Variables and macros

Document-wide variables are defined in `Awal_konfigurasi.tex`. Use these macros in content files, do not hardcode values:

| Macro | Purpose |
|---|---|
| `\penulis`, `\Penulis` | Author name |
| `\nim` | Student ID |
| `\JudulIndo` | Indonesian title |
| `\pembimbingSatu`, `\pembimbingDua` | Supervisor names |
| `\program`, `\fakultas` | Program and faculty |

`\var{cmd}{value}` creates a simple text macro. `\Var{cmd}{value}` creates an uppercase version (uses `\uppercase{}` internally — hyperref will warn about this in PDF bookmarks, this is a known harmless warning).

---

## 11. Known harmless warnings (do not chase these)

These appear in every CI run and are safe to ignore:

- `Package fancyhdr Warning: \headheight is too small (0.0pt)` — set intentionally in `ta.sty` (`headheight=0pt`)
- `Package hyperref Warning: Token not allowed in a PDF string: removing \uppercase` — from `\Var` macros in chapter headings
- `Package apacite Warning: No suitable language definition file (indonesian.apc) found` — apacite has no Indonesian locale file
- `Package fixltx2e Warning: fixltx2e is not required with releases after 2015` — legacy package included in `ta.sty`
- `Underfull \hbox / \vbox` — line-breaking issues in narrow table columns, cosmetic only

---

## 12. File ownership / do not modify

These files are part of the BINUS thesis template and should not be changed unless you fully understand TeX package internals:

- `ta.sty` — main thesis style (margins, fonts, float placement, custom commands)
- `fncychap.sty`, `algorithm*.sty`, `idhypen.sty` — bundled packages
- `fncychap.sty` uses `ConnyRevised` chapter style

---

## 13. Gitignore rules

`build/*` is gitignored **except** `build/Skripsi.pdf` (via `!build/Skripsi.pdf`).

- Never commit `.aux`, `.log`, `.toc`, `.lof`, `.lot`, `.bbl`, `.blg`, `.synctex.gz` files
- The CI commits `build/Skripsi.pdf` automatically — do not manually add it to commits
- Do not change the gitignore pattern without also updating the CI workflow `paths-ignore`