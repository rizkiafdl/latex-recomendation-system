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

## 14. Never add `\DocumentMetadata{...}` to Skripsi.tex — CRITICAL

Do **not** add `\DocumentMetadata{tagging=off}` (or any `\DocumentMetadata{}`) before `\documentclass` in `Skripsi.tex`.

**Why this breaks CI:** `\DocumentMetadata` loads ALL `latex-lab-testphase-*` modules, including `latex-lab-testphase-bib.sty`. That module tries to add a hook to `\@lbibitem/before`, but `apacite`'s version of `\@lbibitem` uses `#1`/`#2` parameters. This causes:
```
Illegal parameter number in definition of hook 'cmd/@lbibitem/before'.
Offending label: 'latex-lab-testphase-bib'.
```
pdflatex exits with code 1 → CI fails even if a PDF is produced.

This `\DocumentMetadata` was previously added as a workaround for nested `longtable` errors, but that issue is now fixed structurally (inner tables converted to `tabular`). The workaround is never needed again.

---

## 15. Never nest `longtable` inside a `minipage` inside another `longtable` — CRITICAL

In TeX Live 2026, using `longtable` inside a `minipage` cell of an outer `longtable` causes a fatal error:
```
! Forbidden control sequence found while scanning use of \LT@nofcols.
<recently read> \par
```

**Wrong:**
```latex
SomeColumn & \begin{minipage}[t]{\linewidth}\raggedright
\begin{longtable}{@{}ll@{}}   % <-- nested longtable: FATAL in TeX Live 2026
...
\end{longtable}
\end{minipage} \\
```

**Correct:** Use `tabular` for any table that lives inside a `minipage` cell:
```latex
SomeColumn & \begin{minipage}[t]{\linewidth}\raggedright
\begin{tabular}{@{}ll@{}}
\toprule\noalign{}
...
\bottomrule\noalign{}
\end{tabular}
\end{minipage} \\
```

**Key differences when converting `longtable` → `tabular` inside minipage:**
- Drop `[]` from `\begin{longtable}[]` → `\begin{tabular}`
- Remove `\endhead`, `\endlastfoot` lines (not valid in tabular)
- Remove `\bottomrule\noalign{}` that precedes `\endlastfoot` (add it before `\end{tabular}` instead)
- Replace `\end{longtable}` → `\end{tabular}`

**Root cause:** TeX Live 2026's `longtable` injects `\UseTagging` calls (PDF tagging hooks) via `\LT@bchunk`. When scanning the inner longtable's column spec, the outer table's L3 hook fires and injects `\par`, which `\LT@nofcols` (column-spec scanner) rejects as a forbidden control sequence.

---

## 16. No Unicode characters in LaTeX math mode — CRITICAL

When adding or editing equations in `.tex` files, **never paste Unicode subscript, superscript, or special math characters** into `\[...\]` or `$...$` math environments. pdflatex cannot typeset them.

**Forbidden Unicode characters (common pandoc/Word artifacts):**

| Character | Unicode | Wrong | Correct LaTeX |
|-----------|---------|-------|---------------|
| Latin subscript i | U+1D62 `ᵢ` | `Hit@Kᵢ` | `Hit@K_i` |
| Superscript K | U+1D37 `ᴷ` | `^ᴷ` | `^{K}` |
| Subscript equals | U+208C `₌` | `₌₁` | `_{i=1}` |
| Subscript 1 | U+2081 `₁` | `₌₁` | `_{i=1}` |
| Subscript 2 | U+2082 `₂` | `log₂` | `\log_2` |
| Unicode minus | U+2212 `−` | `5 − 1` | `5 - 1` |
| Right arrow | U+2192 `→` | `A → B` (in text or table) | `$\rightarrow$` |

**How to detect before committing:**
```bash
python3 -c "
import sys, unicodedata
text = open(sys.argv[1]).read()
for i,ch in enumerate(text):
    if ord(ch) > 127 and unicodedata.category(ch).startswith('L') or ord(ch) in range(0x2000,0x2200):
        print(f'pos {i}: U+{ord(ch):04X} {repr(ch)} — {unicodedata.name(ch,\"?\")}')" bab2.tex
```

**Why this happens:** pandoc converts `.docx` Word files preserving Unicode characters from Word's equation editor. Always audit `.tex` files produced by pandoc before committing.

---

## 17. No blank line between `\raggedright` and `\begin{longtable}` inside minipage

Even if the outer/inner longtable nesting issue is resolved structurally, as a defensive rule: never leave a blank line between `\begin{minipage}[t]{...}\raggedright` and `\begin{longtable}`.

**Wrong:**
```latex
\begin{minipage}[t]{\linewidth}\raggedright

\begin{longtable}{...}
```

**Correct:**
```latex
\begin{minipage}[t]{\linewidth}\raggedright
\begin{longtable}{...}
```

**Why:** A blank line generates `\par`. Inside TeX Live 2026's longtable column-spec scanner, `\par` is a forbidden control sequence that causes a fatal error.

---

## 13. Gitignore rules

`build/*` is gitignored **except** `build/Skripsi.pdf` (via `!build/Skripsi.pdf`).

- Never commit `.aux`, `.log`, `.toc`, `.lof`, `.lot`, `.bbl`, `.blg`, `.synctex.gz` files
- The CI commits `build/Skripsi.pdf` automatically — do not manually add it to commits
- Do not change the gitignore pattern without also updating the CI workflow `paths-ignore`