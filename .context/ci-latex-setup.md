# CI LaTeX Workflow Setup

## What was set up
- Git repo initialized on `main` branch
- GitHub remote: `git@github.com:rizkiafdl/latex-recomendation-system.git`
- GitHub Actions workflow: `.github/workflows/compile.yml`
  - Triggers on every push to `main` (except when the PDF itself is committed)
  - Uses `xu-cheng/latex-action@v3` with TeX Live 2026 (latest)
  - Compiles `Skripsi.tex` via latexmk (reads `.latexmkrc`)
  - Commits `build/Skripsi.pdf` back to the repo via `stefanzweifel/git-auto-commit-action@v5`
- `.gitignore` updated: ignores all `build/*` artifacts except `build/Skripsi.pdf`

## Local workflow
- VS Code + LaTeX Workshop extension (`.vscode/settings.json`)
- Auto-build on save, PDF preview in tab
- Output goes to `build/Skripsi.pdf`
- `.latexmkrc` configures pdflatex + bibtex into `build/`

---

# CI Failure & Fix (first run)

## Error encountered
```
./Skripsi.tex:201: LaTeX Error: \begin{center} on input line 7 ended by \end{document}.
Fatal error occurred, no output PDF file produced!
```

## Root cause 1 — `-halt-on-error` killing multi-pass compilation
LaTeX needs multiple passes (pdflatex → bibtex → pdflatex) to resolve TOC, LOF, LOT, and bibliography. On the first pass, `.toc`/`.lof`/`.lot` files don't exist yet — this is normal. The `xu-cheng/latex-action` default args include `-halt-on-error`, which exits pdflatex immediately on any error, preventing latexmk from running the second/third pass.

**Fix**: Override `args` in the workflow to remove `-halt-on-error` and add `-f` (latexmk force mode):
```yaml
args: -pdf -file-line-error -interaction=nonstopmode -f
```

## Root cause 2 (actual) — unmatched `\begingroup` in orisinalitas.tex and pengesahan.tex

**This was the real fatal error.** Both files had this pattern:

```latex
\begin{center}      % internally calls \begingroup → opens group A
    \begingroup     % explicitly opens group B — NO MATCHING \endgroup!
    \begin{tabular}{...}
    ...
    \end{tabular}
\end{center}        % calls \endgroup → closes group B (not A!)
                    % group A is now permanently unclosed
```

Because `\end{center}` closes the most recently opened `\begingroup`, and there's an explicit `\begingroup` between `\begin{center}` and `\end{center}`, the center's own group leaks out. This persists until `\end{document}` where LaTeX reports it as a fatal error.

The TeX debug output confirmed it: `\end occurred inside a group at level 2` with groups opened at lines 59 (orisinalitas.tex `\begin{center}`) and 7 (pengesahan.tex `\begin{center}`).

**Fix**: add `\endgroup` before each `\end{center}` that follows an unmatched `\begingroup`:
- `orisinalitas.tex:66` — added `\endgroup` after `\end{tabular}` at line 65
- `pengesahan.tex:38` — added `\endgroup` after `\end{tabular}` at line 37

## Root cause 3 (minor) — `\begin{center}` inside `\begin{figure}` (TeX Live 2026 strictness)
All figures in `sampul.tex`, `sampul2.tex`, and `bab4.tex` used this pattern:
```latex
\begin{figure}
    \begin{center}
        \includegraphics[...]{pic/xxx.png}
    \end{center}
\end{figure}
```

`\begin{center}` is a named environment with open/close tracking. Inside `\begin{figure}[H]` (H placement from `float.sty`, set as default in `ta.sty`), TeX Live 2026 loses track of the open center environment and reports it as unclosed at `\end{document}`.

**Fix**: Replace with `\centering` (a declaration, not an environment — no tracking needed):
```latex
\begin{figure}
    \centering
    \includegraphics[...]{pic/xxx.png}
\end{figure}
```

Visual output is identical. Applies to 10 figures total across 3 files.

## Why it "worked" on Overleaf but not CI
- Overleaf uses **TeX Live 2023** by default — more lenient about this pattern
- GitHub CI uses **TeX Live 2026** — stricter environment tracking
- Overleaf uses `-interaction=nonstopmode` (continue past errors); CI added `-halt-on-error` on top
- Same document, different engine version = different strictness

## Files changed in the fix
- `.github/workflows/compile.yml` — added `args: -pdf -file-line-error -interaction=nonstopmode -f`
- `sampul.tex` — figure #1: center → centering
- `sampul2.tex` — figure #1: center → centering
- `bab4.tex` — figures #1–8 (login, register, dashboard, datacenter, runhistory, rundetails, supervisorstudio, rulestudio): center → centering

---

# CI Failure (runs #5 & #6) — Post-Migration Errors

These errors were introduced by the pandoc migration of `Formated Version 20251220.docx` into bab1–3.tex. Run #5 had two fatal errors; run #6 still failed because `\DocumentMetadata` alone wasn't enough for the longtable issue.

## Error 1 — Unicode subscript/superscript/minus in math equations (bab2.tex)

**Root cause**: pandoc preserved Unicode characters from Word's equation editor as raw UTF-8:
- `ᵢ` (U+1D62) — Latin subscript small letter i — inside `\[...\]` math
- `ᴷ` (U+1D37) — superscript K
- `₌₁` (U+208C U+2081) — subscript equals, subscript one
- `₂` (U+2081) — subscript two inside `log₂`
- `−` (U+2212) — Unicode minus sign (not ASCII `-`)

pdflatex does not know how to typeset these characters in math mode and treats them as unknown control sequences, causing a fatal error.

**Fix applied to bab2.tex**:
| Line | Before | After |
|------|--------|-------|
| 181 | `Hit@Kᵢ` | `Hit@K_i` |
| 187 | `\Sigma ᵢ₌₁ᴷ\ (\frac{relᵢ}{log₂}(i + 1))` | `\sum_{i=1}^{K} \left(\frac{rel_i}{\log_2(i + 1)}\right)` |
| 225 | `\Sigma\ RRᵢ` | `\Sigma\ RR_i` |
| 231 | `RRᵢ adalah...` (body text) | `$RR_i$ adalah...` |
| 251 | `jawaban − 1` (U+2212) | `jawaban - 1` (ASCII) |
| 253 | `5 − jawaban` (U+2212) | `5 - jawaban` (ASCII) |

Also fixed in bab3.tex:
- Line 755: `→` (U+2192 right arrow in table cell body) → `$\rightarrow$`

## Error 2 — Nested longtable inside minipage inside longtable cell (bab3.tex)

**Root cause**: pandoc generated use-case narrative tables as outer `longtable` environments. The "Flow Of Events" and "Exception Condition" columns contained a `\begin{minipage}[t]{\linewidth}\raggedright` followed by a blank line, then an inner `\begin{longtable}`. Structure:

```latex
Flow Of Events & \begin{minipage}[t]{\linewidth}\raggedright
                                             ← blank line → generates \par
\begin{longtable}[]{@{}...@{}}
```

In TeX Live 2026, the updated longtable package includes PDF tagging support (`\UseTagging` injected into `\LT@bchunk`). During column-spec scanning (`\LT@nofcols`), the `\par` token generated by the blank line is a forbidden control sequence in that context:

```
! Forbidden control sequence found while scanning use of \LT@nofcols.
<recently read> \par
l.393
```

This pattern appears 14 times across all use-case narrative tables (7 use cases × 2 columns each).

**Fix 1 — `Skripsi.tex`**: Added `\DocumentMetadata{tagging=off}` before `\documentclass` to disable TeX Live 2026 PDF tagging. This prevents `\UseTagging` from being injected:
```latex
\DocumentMetadata{tagging=off}
\documentclass[12pt, a4paper, onecolumn, twoside, final]{report}
```

**Fix 2 — `bab3.tex`**: Removed all 14 blank lines between `\raggedright` and `\begin{longtable}` inside the `minipage[t]` cells. Used Python regex to do this in one pass:
```python
re.sub(
    r'(\\begin\{minipage\}\[t\]\{[^}]*\}\\raggedright)\n\n+(\s*\\begin\{longtable\})',
    r'\1\n\2',
    content
)
```

Both fixes together (belt-and-suspenders) ensure the error cannot recur if the TeX Live version changes again.

## Summary of files changed for CI fixes
- `Skripsi.tex` — added `\DocumentMetadata{tagging=off}` before `\documentclass`
- `bab2.tex` — 6 Unicode math character substitutions
- `bab3.tex` — removed 14 blank lines before inner longtables + replaced `→` with `$\rightarrow$`
