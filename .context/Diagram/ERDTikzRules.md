# TikZ Rules — Entity Relationship Diagram (fig3.4)

Source: `bab3.tex` — the ERD `figure[H]` block after the class diagram section.
Semantic content: Mermaid ERD in [[Diagram.md]]; table schema in [[ClassDiagram.md]].

---

## Global TikZ Style Block

```latex
\begin{tikzpicture}[
  font=\footnotesize,
  entity/.style={rectangle, inner sep=0pt},
  rel/.style={draw, thick},
  lab/.style={font=\scriptsize\bfseries, fill=white, inner sep=2pt}
]
```

| Key | Value | Note |
|-----|-------|------|
| `font` | `\footnotesize` | Larger than class diagrams (`\scriptsize`) — ERD tables are dense |
| `entity` style | `rectangle, inner sep=0pt` | Same pattern as `class` in class diagram |
| `rel` style | `draw, thick` | Plain solid thick lines — no arrowheads |
| `lab` style | `\scriptsize\bfseries, fill=white, inner sep=2pt` | Cardinality labels; `inner sep=2pt` (vs 1pt in class diagram) |

Outer wrapper: `\resizebox{\textwidth}{!}{%  ... }`.

---

## Node Structure — Entity Box

```latex
\node (students) [entity] at (0, 0) {%
  \begin{tabular}{|p{4.0cm}|}
  \hline
  \multicolumn{1}{|c|}{\textbf{table\_name}} \\ \hline  % table name row + separator
  \underline{\textbf{id}} : Integer \\                   % PK: underlined bold
  \textit{run\_id} : Integer (FK) \\                     % FK: italic
  field\_name : Type \\                                  % plain field
  \hline
  \end{tabular}%
};
```

### Field encoding
| Field type | LaTeX formatting | Example |
|------------|-----------------|---------|
| Primary Key | `\underline{\textbf{field}} : Type` | `\underline{\textbf{id}} : Integer` |
| Foreign Key | `\textit{field} : Type (FK)` | `\textit{run\_id} : Integer (FK)` |
| Regular field | plain text | `name : String` |

### Column width guidelines
| Table | Width |
|-------|-------|
| students | `p{4.0cm}` |
| recommendations | `p{4.2cm}` |
| recommendation\_runs | `p{5.0cm}` |
| supervisors | `p{3.5cm}` |
| app\_users | `p{3.5cm}` |

Use wider `p{...}` for tables with many long field names.

---

## Layout — Node Coordinates

```
students             at (0,   0)    ← left column
recommendations      at (6,   0)    ← centre column (junction table)
recommendation_runs  at (12,  0)    ← right column
supervisors          at (6,  -8)    ← below recommendations
app_users            at (12, -12)   ← below recommendation_runs
```

Three-column top row (left-centre-right), then two subordinate nodes hang below their parents.

---

## Relationship Lines

```latex
%% students (1) -- (N) recommendations
\draw [rel] (students.east) --
  node[lab, above, pos=0.15] {1}
  node[lab, above, pos=0.85] {N}
  (rec.west);
```

### Rules
- Use `[rel]` style — thick solid line, no arrowhead.
- Cardinality labels: two `node[lab, ...]` on the same `\draw` command.
- Label position: `pos=0.15` near the source, `pos=0.85` near the target.
- Label anchor: `above` for horizontal lines; `right` for vertical lines.
- Always write the label pair in source-first order (1 near source, N near target or vice-versa).

### All five relationships in this ERD

| Source | Target | Source card | Target card | Line path |
|--------|--------|-------------|-------------|-----------|
| students | recommendations | `1` (pos=0.15 above) | `N` (pos=0.85 above) | `.east` → `.west` |
| recommendation\_runs | recommendations | `1` (pos=0.15 above) | `N` (pos=0.85 above) | `.west` → `.east` |
| supervisors | recommendations | `1` (pos=0.15 right) | `N` (pos=0.85 right) | `.north` → `.south` |
| app\_users | recommendation\_runs | `N` (pos=0.15 right) | `1` (pos=0.85 right) | `.north` → `.south` |

---

## Figure Caption

```latex
\caption{Entity Relationship Diagram}
\label{fig:fig3.4}   % (or whichever label number bab3.tex uses)
```

---

## Replication Checklist

1. Use `font=\footnotesize` globally (not `\scriptsize` like class diagrams).
2. `inner sep=0pt` on entity nodes — ensures tabular border flush with node border.
3. PK field: `\underline{\textbf{id}}`. FK field: `\textit{field\_name}`.
4. No arrowheads on relationship lines — just `[rel]` (thick, draw).
5. Cardinality labels: `node[lab, above/right, pos=0.15]{card}` + `node[lab, above/right, pos=0.85]{card}` on the same `\draw`.
6. Junction table (`recommendations`) sits at centre; all FKs point into it.
7. Wrap in `\resizebox{\textwidth}{!}{%` … `}`.
