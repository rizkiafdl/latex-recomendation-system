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
