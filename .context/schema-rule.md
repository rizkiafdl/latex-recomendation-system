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

## 16. No Unicode characters in text or math mode — CRITICAL

When adding or editing any `.tex` file, **never paste Unicode special characters** — whether in prose, table cells, `\textbf{}`, or math environments (`$...$`, `\[...\]`). pdflatex cannot typeset them and will exit with code 1, preventing latexmk from completing its second pass and causing all cross-references to appear undefined.

**Forbidden Unicode characters (common pandoc/Word/copy-paste artifacts):**

| Character | Unicode | Wrong | Correct LaTeX |
|-----------|---------|-------|---------------|
| Greek delta (uppercase) | U+0394 `Δ` | `Δ\%Rank-1` (in table cell) | `$\Delta$\%Rank-1` |
| Latin subscript i | U+1D62 `ᵢ` | `Hit@Kᵢ` | `Hit@K_i` |
| Superscript K | U+1D37 `ᴷ` | `^ᴷ` | `^{K}` |
| Subscript equals | U+208C `₌` | `₌₁` | `_{i=1}` |
| Subscript 1 | U+2081 `₁` | `₌₁` | `_{i=1}` |
| Subscript 2 | U+2082 `₂` | `log₂` | `\log_2` |
| Unicode minus | U+2212 `−` | `5 − 1` | `5 - 1` |
| Right arrow | U+2192 `→` | `A → B` (in text or table) | `$\rightarrow$` |

**Common symptom for text-mode Unicode (e.g. Δ in table header):**
```
./bab4.tex:144: LaTeX Error: Unicode character Δ (U+0394) not set up for use with LaTeX.
```
pdflatex returns code 1 → latexmk aborts → second pass never runs → **all `\ref{}` labels appear undefined** even though they are actually defined. The cascade of "undefined reference" warnings is a red herring caused by the Unicode crash.

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

## 18. Escape special characters in regular text — CRITICAL

LaTeX reserves several characters that **must be escaped** when used in normal prose (outside math mode or `\verb`/`lstlisting`):

| Character | Wrong | Correct |
|-----------|-------|---------|
| Underscore `_` | `apple_mobile` | `apple\_mobile` |
| Percent `%` | `50% accuracy` | `50\% accuracy` |
| Ampersand `&` | `R&D` | `R\&D` |
| Hash `#` | `#1 result` | `\#1 result` |
| Dollar `$` | `$100` | `\$100` |
| Caret `^` | `10^6` (in text) | `10\^{}6` or `$10^6$` |
| Tilde `~` | `file~name` | `file\~{}name` |
| Curly braces `{ }` | `{value}` (in text) | `\{value\}` |
| Backslash `\` | `path\file` | `path\textbackslash{}file` |

**Most common real-world offender:** underscores in identifiers, file names, variable names, or URLs pasted into prose (e.g. `user_id`, `apple_mobile`, `binus_bandung`, `my_function`).

**Symptom when broken:**
```
! Missing $ inserted.
<inserted text>
                $
l.349 ...tegori penugasan tertentu (seperti apple_
                                                  mobile ...
```
pdflatex exits code 1 → latexmk exits code 12 → CI fails.

**Quick audit before committing:**
```bash
grep -n '[^\\]_' bab4.tex | grep -v '\\begin\|\\end\|label\|caption\|ref{\|cite{\|includegraphics\|hline\|cline'
```

**Exception:** inside `$...$`, `\[...\]`, `\verb|...|`, or `lstlisting` environments, `_` is valid and must NOT be escaped.

---

## 19. TikZ flowchart diagrams

Use TikZ (not image files) for flowcharts and diagrams where possible. TikZ is already loaded via `ta.sty`.

### Required libraries

Add to `ta.sty` after `\usepackage{tikz}` (already done):
```latex
\usetikzlibrary{shapes.geometric,shapes.misc,positioning}
```

- `shapes.geometric` — provides `diamond` (decision) and `trapezium` (I/O) shapes
- `shapes.misc` — provides `rounded rectangle` (terminal/start/end shapes)
- `positioning` — enables `below=of <node>` relative placement syntax

### Standard flowchart node styles

```latex
terminal/.style={draw, rounded rectangle, text width=2cm, align=center,
  minimum height=0.55cm, font=\footnotesize}   % Mulai / Selesai

process/.style={draw, rectangle, text width=5.5cm, align=center,
  minimum height=0.7cm, inner sep=4pt, font=\footnotesize}   % algorithm steps

io/.style={draw, trapezium, trapezium left angle=75, trapezium right angle=105,
  text width=4.8cm, align=center,
  minimum height=0.7cm, inner sep=4pt, font=\footnotesize}   % input/output steps

decision/.style={draw, diamond, aspect=2.5, text width=1.6cm, align=center,
  inner sep=2pt, font=\footnotesize}   % decision diamond
```

For the parallelogram (I/O) shape: `trapezium left angle=75, trapezium right angle=105` makes both sides slant in the same direction (75 + 105 = 180 → parallel sides → true parallelogram).

### Keeping diagrams within page bounds

Tall flowcharts (10+ nodes) overflow the page. Always wrap in `\scalebox`:

```latex
\begin{figure}[H]
\centering
\scalebox{0.75}{%
\begin{tikzpicture}[node distance=0.3cm, ...]
  ...
\end{tikzpicture}%
}
\caption{...}
\end{figure}
```

Use `\scalebox{0.75}` as the default starting point. Drop to `0.70` if still overflowing.

### Feedback/loop arrows

For a "Tidak" branch that loops back to an earlier node:

```latex
\draw [arrow] (decide.east)
  -- node[above, font=\footnotesize] {Tidak} ++(2.8,0)
  |- (input2.east);
```

The `-- ++(2.8,0)` goes right, then `|-` means vertical-then-horizontal, routing back up the right side to the target node's east anchor.

---

## 13. Gitignore rules

`build/*` is gitignored **except** `build/Skripsi.pdf` (via `!build/Skripsi.pdf`).

- Never commit `.aux`, `.log`, `.toc`, `.lof`, `.lot`, `.bbl`, `.blg`, `.synctex.gz` files
- The CI commits `build/Skripsi.pdf` automatically — do not manually add it to commits
- Do not change the gitignore pattern without also updating the CI workflow `paths-ignore`

---

## 20. Use Case Narrative table pattern

Use case narrative tables use a **3-column longtable** with full `\hline` borders (Word style). Most rows merge cols 2+3 via `\multicolumn`; Flow Of Events and Exception Condition rows use all 3 columns directly — no nested tabulars, no minipage wrappers.

### Column spec

```latex
\begin{longtable}{|p{3.5cm}|p{4.5cm}|p{5cm}|}
```

- Col 1 (3.5cm): field names (Elemen)
- Col 2 (4.5cm): Actor / Condition sub-column
- Col 3 (5.0cm): System / Handling sub-column

### Multicolumn width fix — CRITICAL

`\multicolumn{2}{p{9.5cm}|}` is slightly narrower than cols 2+3 combined because it misses the internal `|` (0.4pt) and its surrounding `\tabcolsep` pads (2×6pt). Always define the corrected length before the table:

```latex
\newlength{\ucdeskcol}
\setlength{\ucdeskcol}{\dimexpr 9.5cm + 2\tabcolsep + \arrayrulewidth\relax}
```

Then use `\multicolumn{2}{p{\ucdeskcol}|}` for all merged-column rows.

### Header: first page only

```latex
\caption{Tabel X.Y Use Case <Name>} \label{table:tabX.Y} \\
\hline
\textbf{Elemen} & \multicolumn{2}{p{\ucdeskcol}|}{\textbf{Deskripsi}} \\
\hline
\endfirsthead
\endhead          % empty — no repeated header on continuation pages
\hline
\endfoot
```

### Full skeleton

```latex
\newlength{\ucdeskcol}
\setlength{\ucdeskcol}{\dimexpr 9.5cm + 2\tabcolsep + \arrayrulewidth\relax}
\begin{longtable}{|p{3.5cm}|p{4.5cm}|p{5cm}|}
\caption{Tabel X.Y Use Case <Name>} \label{table:tabX.Y} \\
\hline
\textbf{Elemen} & \multicolumn{2}{p{\ucdeskcol}|}{\textbf{Deskripsi}} \\
\hline
\endfirsthead
\endhead
\hline
\endfoot
Use Case Name   & \multicolumn{2}{p{\ucdeskcol}|}{<name>} \\
\hline
Scenario        & \multicolumn{2}{p{\ucdeskcol}|}{<scenario>} \\
\hline
Triggering Events & \multicolumn{2}{p{\ucdeskcol}|}{<trigger>} \\
\hline
Brief Description & \multicolumn{2}{p{\ucdeskcol}|}{<description>} \\
\hline
Actor           & \multicolumn{2}{p{\ucdeskcol}|}{<actor>} \\
\hline
Related Use Case & \multicolumn{2}{p{\ucdeskcol}|}{<related>} \\
\hline
Stakeholder     & \multicolumn{2}{p{\ucdeskcol}|}{<stakeholder>} \\
\hline
Pre-condition   & \multicolumn{2}{p{\ucdeskcol}|}{<pre>} \\
\hline
Post-condition  & \multicolumn{2}{p{\ucdeskcol}|}{<post>} \\
\hline
Flow Of Events  & \textbf{Actor} & \textbf{System} \\
\hline
 & <actor action 1> & <system response 1> \\
\hline
 & <actor action 2> & <system response 2> \\
\hline
Exception Condition & \textbf{Condition} & \textbf{Handling} \\
\hline
 & <condition 1> & <handling 1> \\
\hline
\end{longtable}
```

### Rules
- `\newlength{\ucdeskcol}` must appear **before** the longtable, not inside it. `\newlength` errors if the name is already defined — declare it exactly once per file (before the first use case table). For safety when copy-pasting across files use a guard: `\ifdefined\ucdeskcol\else\newlength{\ucdeskcol}\fi`.
- Never use nested `tabular` or `minipage` inside the cells — use the 3-column flat structure.
- Flow Of Events and Exception Condition sub-rows leave col 1 empty (`& ...`).
- Labels follow the convention `table:tabX.Y` (e.g. `table:tab3.6`).

---

## 21. TikZ Use Case diagrams

Use TikZ for UML Use Case diagrams. TikZ and the required libraries are already loaded in `ta.sty`.

### Required node styles

```latex
\begin{tikzpicture}[
  font=\footnotesize,
  actor/.style={text centered, font=\footnotesize},
  usecase/.style={draw, ellipse, text width=2.5cm, align=center,
    minimum height=1cm, font=\footnotesize},
  assoc/.style={-},
  incl/.style={->, >=stealth, dashed},
  arr/.style={->, >=stealth}
]
```

- `actor` — plain text label (no shape); place outside the system boundary box
- `usecase` — ellipse node; place inside the system boundary box
- `assoc` — solid line, actor ↔ use case association
- `incl` — dashed arrow for `«include»` and `«extend»` relationships

### Actor (stick figure)

Draw the stick figure manually above the actor label node:

```latex
% Stick figure at absolute (x, y)
\draw (x, y+0.55) circle (0.18cm);                        % head
\draw (x, y+0.37) -- (x, y-0.1);                          % body
\draw (x-0.22, y+0.18) -- (x, y+0.28) -- (x+0.22, y+0.18); % arms
\draw (x, y-0.1) -- (x-0.18, y-0.45);                     % left leg
\draw (x, y-0.1) -- (x+0.18, y-0.45);                     % right leg
\node [actor] at (x, y-0.65) {Actor Name};
```

### System boundary

Draw the boundary as an explicit rectangle, with a label anchored to its north-west corner:

```latex
\draw[thick] (0.5, 0.3) rectangle (10.5, -8.5);
\node[anchor=north west, font=\small\bfseries] at (0.5, 0.3) {System Name};
```

All `usecase` nodes must be placed **inside** this rectangle's coordinate range.

### Include / Extend relationships

```latex
% «include»
\draw[incl] (uc1) -- node[above, font=\tiny]{«include»} (uc2);

% «extend»
\draw[incl] (uc3) -- node[above, font=\tiny]{«extend»} (uc4);
```

Arrow points **from** the base use case **to** the included/extended one (UML convention).

### Full skeleton

```latex
\begin{figure}[H]
\centering
\scalebox{0.80}{%
\begin{tikzpicture}[
  font=\footnotesize,
  actor/.style={text centered, font=\footnotesize},
  usecase/.style={draw, ellipse, text width=2.5cm, align=center,
    minimum height=1cm, font=\footnotesize},
  assoc/.style={-},
  incl/.style={->, >=stealth, dashed}
]

%% System boundary
\draw[thick] (0.5, 0.3) rectangle (10.5, -8.5);
\node[anchor=north west, font=\small\bfseries] at (0.5, 0.3) {Sistem Rekomendasi};

%% Actors (left side, outside boundary)
\draw (-1.5, 0.35) circle (0.18cm);
\draw (-1.5, 0.17) -- (-1.5, -0.28);
\draw (-1.72, -0.02) -- (-1.5, 0.08) -- (-1.28, -0.02);
\draw (-1.5, -0.28) -- (-1.68, -0.63);
\draw (-1.5, -0.28) -- (-1.32, -0.63);
\node [actor] at (-1.5, -0.85) {EPC};

%% Use cases (inside boundary)
\node (uc1) [usecase] at (5.5, -1.5) {Login};
\node (uc2) [usecase] at (5.5, -4.0) {Generate Rekomendasi};
\node (uc3) [usecase] at (5.5, -6.5) {Export Excel};

%% Associations
\draw[assoc] (-1.5, -0.65) -- (uc1);
\draw[assoc] (-1.5, -0.65) -- (uc2);
\draw[assoc] (-1.5, -0.65) -- (uc3);

%% Include / Extend
\draw[incl] (uc2) -- node[right, font=\tiny]{«include»} (uc1);

\end{tikzpicture}%
}
\caption{Use Case Diagram}
\label{fig:figX.Y}
\end{figure}
```

### Sizing rules

| Diagram size | Recommended scale |
|---|---|
| ≤ 8 use cases | `\scalebox{0.80}` |
| 9–15 use cases | `\scalebox{0.70}` |
| 16+ use cases | Split into sub-diagrams per subsystem |

- Keep all actor nodes outside (left/right of) the system boundary rectangle
- Keep all use case ellipses inside the boundary
- Use elbow routing (`-- ++(dx,0) |-`) for association lines that would otherwise cross
- Do **not** use `\begin{center}` inside `\begin{figure}` — use `\centering` (Rule 2)