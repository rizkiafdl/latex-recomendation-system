# TikZ Rules — Use Case Diagram (fig3.2)

Source: `.context/Diagram/use-case.tex` (canonical TikZ), `bab3.tex`.
Semantic content: [[UseCaseDiagramDescription.md]]
Activity flows: [[ActivityDiagramDescription.md]]

---

## Global TikZ Style Block

```latex
\begin{tikzpicture}[
  font=\scriptsize,
  uc/.style={ellipse, draw, text width=4.0cm, align=center,
             minimum height=0.9cm, inner sep=3pt},
]
```

| Key | Value | Purpose |
|-----|-------|---------|
| `font` | `\scriptsize` | All labels in the diagram |
| `uc` style | `ellipse, draw, text width=4.0cm, align=center, minimum height=0.9cm, inner sep=3pt` | Every use case node |

Outer wrapper: `\scalebox{0.65}{%  ... }` (not `\resizebox` — fixed scale to fit A4).

---

## System Boundary

```latex
\draw[thick] (1.0, 1.0) rectangle (10.5, -16.2);
\node[font=\scriptsize\bfseries, above] at (5.75, 1.0)
  {Sistem Rekomendasi Faculty Supervisor};
```

- Top-left corner: `(1.0, 1.0)`
- Bottom-right: `(10.5, -16.2)` — sized for **11 use cases** at y-spacing 1.5 units
- Title node: centred at `x = (1.0 + 10.5) / 2 = 5.75`, placed `above` the top edge
- If use case count changes: bottom y = `1.0 - 1.0 - (N-1)*1.5 - 1.2` where N = number of UCs

---

## Use Case Nodes

```latex
\node (uc1)  [uc] at (6.5,   0.0) {Register};
\node (uc2)  [uc] at (6.5,  -1.5) {Login};
% ... etc.
\node (uc11) [uc] at (6.5, -15.0) {Logout};
```

- All nodes at **x = 6.5** (single column, centred inside the boundary)
- y = 0.0 for first, then **−1.5 per step**
- 11 use cases: y range `[0, -15.0]`, vertical centre at `y = -7.5`

### Current use case list (updated 2026-06-17)
| # | Node id | Label | y |
|---|---------|-------|---|
| 1 | `uc1` | Register | 0.0 |
| 2 | `uc2` | Login | -1.5 |
| 3 | `uc3` | Import Data Mahasiswa | -3.0 |
| 4 | `uc4` | Kelola Data Faculty Supervisor | -4.5 |
| 5 | `uc5` | Kelola Keywords Supervisor | -6.0 |
| 6 | `uc6` | Lihat Dashboard | -7.5 |
| 7 | `uc7` | Trigger Proses Rekomendasi | -9.0 |
| 8 | `uc8` | Lihat Riwayat Run | -10.5 |
| 9 | `uc9` | Lihat Hasil Rekomendasi | -12.0 |
| 10 | `uc10` | Export Hasil ke Excel | -13.5 |
| 11 | `uc11` | Logout | -15.0 |
| 12 | `uc12` | Export Konfigurasi Supervisor | -16.5 |

Actor centre shifted to `(-0.8, -8.25)` (12-UC vertical centre). Fan line origin: `(-0.28, -7.60)`. Boundary bottom: `-18.0`.

---

## Actor (EPC Stick Figure)

Actor centred at `(-0.8, -7.5)` — aligned with vertical centre of use case list.

```latex
%% Offsets from centre (-0.8, -7.5):
%% head:  centre_y + 1.55 = -5.95
%% neck:  centre_y + 1.23 = -6.27
%% arm:   centre_y + 0.65 = -6.85   ← fan-line origin x = arm_tip = -0.8 + 0.52 = -0.28
%% hip:   centre_y + 0.15 = -7.35
%% feet:  centre_y - 0.65 = -8.15

\draw[fill=white] (-0.8, -5.95) circle (0.32cm);  % head (r=0.32)
\draw (-0.8, -6.27) -- (-0.8, -7.35);             % body (neck to hip)
\draw (-1.32, -6.85) -- (-0.28, -6.85);           % arms (span = 1.04)
\draw (-0.8,  -7.35) -- (-1.22, -8.15);           % left leg
\draw (-0.8,  -7.35) -- (-0.38, -8.15);           % right leg
\node[font=\scriptsize\bfseries, below=0.12cm] at (-0.8, -8.15) {EPC};
```

**Right arm tip** = `(-0.28, -6.85)` — this is the origin of all fan lines.

### Scaling formula when moving actor centre
If actor centre moves to `(cx, cy)`:
- head centre: `(cx, cy + 1.55)`
- neck: `cy + 1.23`; body to `cy + 0.15`
- arm y: `cy + 0.65`; left tip `cx - 0.52`; right tip `cx + 0.52`
- feet: `cy - 0.65`; left `cx - 0.42`; right `cx + 0.42`
- label: `below=0.12cm` at `(cx, cy - 0.65)`

---

## Fan Lines (Actor → Use Cases)

```latex
\foreach \uc in {uc1, uc2, uc3, uc4, uc5, uc6, uc7, uc8, uc9, uc10, uc11} {
  \draw (-0.28, -6.85) -- (\uc.west);
}
```

- Origin: right arm tip `(-0.28, -6.85)`
- Target: `.west` anchor of each use case ellipse
- `\foreach` loop — no individual `\draw` per line needed
- Lines are plain (no arrowhead, no style)

---

## Figure Wrapper

```latex
\begin{figure}[H]
\centering
\scalebox{0.65}{%
\begin{tikzpicture}[...]
  ...
\end{tikzpicture}%
}
\caption{Use Case Diagram Sistem Rekomendasi Faculty Supervisor}
\label{fig:fig3.2}
\end{figure}
```

`\scalebox{0.65}` — use this, not `\resizebox{\textwidth}{!}`, to preserve fixed proportions.

---

## Adding a New Use Case

1. Add a new `\node (ucN) [uc] at (6.5, <y>) {Label};` with next y step (−1.5 from last).
2. Add `ucN` to the `\foreach` list.
3. Recompute vertical centre: `new_centre_y = -(N-1)*1.5 / 2` where N = total UCs.
4. Move actor centre y to match new centre.
5. Recalculate all actor y coordinates using the formula above.
6. Extend boundary bottom: `bottom_y = 1.0 - 1.0 - (N-1)*1.5 - 1.2`
7. Update `UseCaseDiagramDescription.md` layout notes section.

---

## Replication Checklist

1. `\scalebox{0.65}` wrapper — not `\resizebox`.
2. `uc` style: ellipse, `text width=4.0cm`, `minimum height=0.9cm`, `inner sep=3pt`.
3. Use case column at `x = 6.5`, spacing `-1.5` per node.
4. Actor right arm tip coordinates drive all fan lines — keep consistent.
5. `\foreach` loop for fan lines — do not hardcode individual `\draw` per use case.
6. System boundary title: `\node[..., above] at (centre_x, top_y)`.
7. Actor label: `\node[..., below=0.12cm]` at feet position.
