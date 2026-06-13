# TikZ Rules — Class Diagrams (fig3.3a / fig3.3b / fig3.3c)

Source: `bab3.tex` — three `figure[H]` blocks.
Semantic content: [[ClassDiagram.md]] and [[ClassDiagramDescription.md]]

---

## Global TikZ Style Block

```latex
\begin{tikzpicture}[
  font=\scriptsize,
  class/.style={rectangle, inner sep=0pt},
  darr/.style={->, >=stealth, dashed},
  lab/.style={font=\scriptsize\bfseries, fill=white, inner sep=1pt}
]
```

| Key | Value | Purpose |
|-----|-------|---------|
| `font` | `\scriptsize` | All text inside the diagram |
| `class` style | `rectangle, inner sep=0pt` | Every class/utility/entity node |
| `darr` style | `->, >=stealth, dashed` | «uses» dependency arrows |
| `lab` style | `\scriptsize\bfseries, fill=white, inner sep=1pt` | Arrow label overlaid on lines |

Outer wrapper: `\resizebox{\textwidth}{!}{%  ... }` to fill column width.

---

## Node Structure — Class Box

Every class is a `\node (id) [class] at (x, y)` containing a `tabular` with this layout:

```latex
\node (svc) [class] at (7.5, 0) {%
  \begin{tabular}{|p{5.5cm}|}
  \hline
  \multicolumn{1}{|c|}{\textit{«service»}} \\        % stereotype row (italic)
  \multicolumn{1}{|c|}{\textbf{ClassName}} \\ \hline % class name row (bold) + separator
  +method_one(...) \\
  +method_two(...) \\
  \hline
  \end{tabular}%
};
```

### Column width guidelines
| Diagram | Node type | `p{...}` width |
|---------|-----------|---------------|
| fig3.3c (service) | RecommenderService | `p{5.5cm}` |
| fig3.3c (service) | engine / excel / repo / eval | `p{4cm}` |
| fig3.3c (service) | embprov / docbld | `p{3.8cm}` |
| fig3.3a / fig3.3b | (use same 3.8–5cm range) | match content width |

### Stereotype labels (first row, `\textit`)
- `«service»` — orchestrator (RecommenderService)
- `«utility»` — stateless module (RecommendationEngine, ExcelIO, Evaluator, DocumentBuilder)
- `«repository»` — DB query module (QueryRepository)
- `«singleton»` — one-instance class with state (EmbeddingProvider)
- `«dataclass»` — frozen value object

### Visibility prefix
- `+` = public method or attribute
- `-` = private (used on `_field` names and `_method()`)

---

## Layout — fig3.3c (Service Architecture, the canonical reference)

```
RecommenderService      at (7.5,   0)    ← top centre
RecommendationEngine    at (1.0, -11)    ← bottom left
ExcelIO                 at (6.0, -11)    ← bottom centre-left
QueryRepository         at (11.0, -11)   ← bottom centre-right
Evaluator               at (16.0, -11)   ← bottom right
EmbeddingProvider       at (0.0, -19)    ← deep left
DocumentBuilder         at (4.5, -19)    ← deep centre-left
```

Three tiers: service (top) → utilities/repo (mid) → sub-utilities (bottom).

---

## Arrows

### Fan from service to mid-tier (all four dependencies from one origin)
```latex
\draw[darr] (svc.south) -- ++(0,-1.5) -| node[lab, near end, right]{uses} (engine.north);
\draw[darr] (svc.south) -- ++(0,-1.5) -| node[lab, near end, right]{uses} (excel.north);
\draw[darr] (svc.south) -- ++(0,-1.5) -| node[lab, near end, right]{uses} (repo.north);
\draw[darr] (svc.south) -- ++(0,-1.5) -| node[lab, near end, right]{uses} (eval.north);
```
Pattern: drop `++(0,-delta)` then turn with `-|` to reach each target.

### Mid-tier to sub-utilities
```latex
\draw[darr] (engine.south) -- ++(0,-1.2) -| node[lab, near end, right]{uses} (embprov.north);
\draw[darr] (engine.south) -- ++(0,-1.2) -| node[lab, near end, right]{uses} (docbld.north);
```
Same pattern, smaller delta (`-1.2` vs `-1.5`).

### Label placement rules
- `node[lab, near end, right]{text}` — label floats right near the receiving end
- `node[lab, near end, left]{text}` — label floats left (use when right overlaps with node)
- `fill=white` on `lab` ensures the label punches through crossing lines

---

## Figure Structure per Sub-Figure

| Figure | Label | Content |
|--------|-------|---------|
| `fig:fig3.3a` | Class Diagram: ORM Models | AppUser, RecommendationRun, Recommendation, Student, Supervisor |
| `fig:fig3.3b` | Class Diagram: Runtime Dataclasses | SupervisorProfile, RunOverrides, CapacityPlan, RecommendationItem, RecommendationOutput, EmbeddingInfo |
| `fig:fig3.3c` | Class Diagram: Service Architecture | RecommenderService + 4 mid-tier + 2 sub-utilities |

Each is a separate `\begin{figure}[H]` block with `\resizebox{\textwidth}{!}`.

---

## Replication Checklist

1. Copy the global style block verbatim — do not change `inner sep=0pt` or `darr` style.
2. Set `p{...}` width wide enough so no method name wraps mid-word.
3. Stereotype row: `\multicolumn{1}{|c|}{\textit{«...»}}` — no `\hline` after it.
4. Class name row: `\multicolumn{1}{|c|}{\textbf{...»}} \\ \hline` — `\hline` separates header from body.
5. Close tabular with `\hline` then `\end{tabular}%`.
6. Arrow labels: always use `[lab, near end, right]` or `[lab, near end, left]` — never bare text.
7. Wrap entire tikzpicture in `\resizebox{\textwidth}{!}{%` … `}`.
