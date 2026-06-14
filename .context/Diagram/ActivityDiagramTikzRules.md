# TikZ Rules — Activity Diagram (Swimlane Style)

Source: `bab3.tex` lines ~1049–1164 (AD-02 Login, canonical reference)
Semantic content: [[ActivityDiagramDescription.md]]
Use case context: [[UseCaseDiagramDescription.md]]

---

## Global TikZ Style Block

```latex
\begin{tikzpicture}[
  font=\small,
  action/.style={
    rectangle, rounded corners=4pt, draw=black, thick,
    text width=3.5cm, align=center, minimum height=1.0cm, fill=white
  },
  decision/.style={
    diamond, draw=black, thick, aspect=1.6,
    text width=2.0cm, align=center, fill=white
  },
  >=latex,
]
```

| Parameter | Value | Reason |
|-----------|-------|--------|
| `font` | `\small` | All node text in \small (not \scriptsize — activity nodes have more text) |
| `action` text width | `3.5cm` | Wide enough to fit 2-line text without overflow (3.0cm caused 4-line wrapping on "Normalisasi username; cari user di database") |
| `action` min height | `1.0cm` | Ensures uniform box height for 2-line nodes |
| `action` corners | `rounded corners=4pt` | Matches reference diagram style |
| `decision` aspect | `1.6` | Makes diamond wider than tall; approx height = text_width / 1.6 ≈ 1.27cm, so half-height ≈ 0.65cm |
| `decision` text width | `2.0cm` | Fits "User\\ ditemukan?" and "Password\\ cocok?" in 2 lines |
| `>=latex` | — | Filled arrowhead on all `->` arrows |

Outer wrapper: `\scalebox{0.65}{%  ... }` — use fixed scale, not `\resizebox`.

---

## Canvas & Swimlane Frame

```latex
%% Outer border
\draw[thick] (0, 0.7) rectangle (12, <bottom>);

%% Title bar (undivided — spans full width)
\draw[thick] (0, -0.1) -- (12, -0.1);
\node[font=\normalsize\bfseries] at (6, 0.3) {<DiagramTitle>};

%% Swimlane header row
\draw[thick] (0, -1.0) -- (12, -1.0);

%% Vertical divider — CRITICAL: start at y=-0.1, NOT y=0.7
%% Starting at 0.7 causes the divider to cut through the title text.
\draw[thick] (5.8, -0.1) -- (5.8, <bottom>);

%% Lane labels
\node[font=\small\bfseries] at (2.9, -0.55) {EPC};
\node[font=\small\bfseries] at (8.9, -0.55) {Sistem};
```

| Element | Value |
|---------|-------|
| Canvas width | `12` units |
| Left border | `x = 0` |
| Right border | `x = 12` |
| Top of canvas | `y = 0.7` |
| Title bar bottom | `y = -0.1` |
| Header row bottom | `y = -1.0` |
| Vertical divider x | `x = 5.8` |
| Divider start y | `y = -0.1` ← **must not be 0.7** |
| EPC lane centre | `x = 2.8` (label at x=2.9) |
| Sistem lane centre | `x = 9.0` (label at x=8.9) |
| Title node centre | `(6, 0.3)` — horizontal centre of full canvas |

Canvas bottom calculation:
```
bottom = last_node_centre_y - 1.5
```
Add ~1.3 units of margin below the end bullseye.

---

## Node Types

### Action Node (rounded rectangle)

```latex
\node (id) [action] at (x, y) {Line one\\Line two};
```

- Always use explicit `\\` for line breaks — do NOT rely on auto-wrapping.
- Keep to **2 lines** maximum per node. If text needs 3+ lines, shorten it.
- `fill=white` ensures nodes cover underlying grid lines cleanly.

### Decision Diamond

```latex
\node (id) [decision] at (x, y) {Question\\word?};
```

- Use `\\` to split the label onto 2 lines.
- Geometry (with `aspect=1.6`, `text width=2.0cm`):
  - Total width ≈ 2.3cm; total height ≈ 1.4cm
  - `node.north` / `node.south` tips: ±0.65cm from centre y
  - `node.east` / `node.west` tips: ±1.15cm from centre x
- **Primary (T) exit**: `.south` — continue downward
- **Alternative (F) exit**: `.west` — go left into EPC lane

### Start Node (filled circle)

```latex
\fill (x, y) circle (0.22cm);
\draw[->] (x, <y + 0.22>) -- (first_node.north);
```

- Placed in **EPC lane** at `x = 2.8`.
- Arrow departs from bottom of circle: `y_start + 0.22`.
- Standard y position: `y = -1.8` (below header row).

### End Node (bullseye)

```latex
\draw[thick] (x, y) circle (0.28cm);   % outer ring
\fill        (x, y) circle (0.15cm);   % inner filled circle
\draw[->] (last_node.south) -- (x, <y + 0.28>);
```

- Placed in **Sistem lane** at `x = 9.0` (or EPC lane if flow ends there).
- Arrow arrives at top of outer ring: `y_end + 0.28`.
- Compute: `y_end + 0.28` = `(last_redir_centre - half_height) - gap - 0.28`; round to clean value.

---

## Arrow Patterns

### Vertical (same lane, straight down)

```latex
\draw[->] (nodeA.south) -- (nodeB.north);
```

Use when two consecutive nodes are in the same lane.

### Horizontal cross-lane (EPC ↔ Sistem)

```latex
\draw[->] (nodeA.east) -- (nodeB.west);   % EPC → Sistem
\draw[->] (nodeA.west) -- (nodeB.east);   % Sistem → EPC (decision F exit)
```

Use when an action triggers a response in the other lane at the **same y level**.

### L-shaped return (Sistem → EPC → down)

```latex
%% System node exits south, bends left, continues down into EPC lane — ONE arrow, NO intermediate arrowhead
\draw[->] (nodeA.south) -- (9.0, <bend_y>) -- (2.8, <bend_y>) -- (nodeB.north);
```

Used to return control to EPC lane after a System response (e.g., "Menampilkan form Login" → EPC fills in the form).

- **Single `\draw[->]`** for the whole path — combining all three segments means the arrowhead only appears at the final destination (`nodeB.north`), not at the corner.
- Do NOT split into two separate `\draw[->]` commands — that puts an unwanted arrowhead at the bend point.
- `bend_y` = midpoint between `nodeA.south` and `nodeB.north`, with at least 0.5 units clearance from each.

### Loop-back arrow (F branch → back to earlier EPC node)

```latex
%% Two F-branch loops both returning to the same target node (click at y=-8.8).
%% Use TikZ |- operator to derive left edge x from the node anchor automatically —
%% avoids manual calculation and the "arrow enters node interior" bug.

%% Helper coordinates: x is irrelevant (|-takes x from click.west); y sets entry height.
\coordinate (cleft_up) at (0, <target_y + 0.25>);   % upper entry, above node centre
\coordinate (cleft_dn) at (0, <target_y - 0.25>);   % lower entry, below node centre

%% First loop-back (err1, gutter x=0.8): enters upper part of target left edge
\draw[->] (err1.west) -- (0.8, <err1_y>) -- (0.8, <target_y + 0.25>) -- (target.west |- cleft_up);

%% Second loop-back (err2, gutter x=0.5): enters lower part of target left edge
\draw[->] (err2.west) -- (0.5, <err2_y>) -- (0.5, <target_y - 0.25>) -- (target.west |- cleft_dn);
```

**How `|-` works:** `(A |- B)` gives a point with x from A and y from B.
So `(target.west |- cleft_up)` = `(target.west.x, cleft_up.y)` — the exact left boundary of the node at the desired y, regardless of inner sep or node size.

- **First loop-back** (closest error): gutter at `x = 0.8`, enters `+0.25` above node centre y
- **Second loop-back** (deeper error): gutter at `x = 0.5`, enters `−0.25` below node centre y
- Do NOT use `target_node.west` for both — they overlap at the same pixel point.
- Do NOT use a hardcoded x like `1.05` — it doesn't account for inner sep and will land inside the node.
- Offset ±0.25 keeps both entries within the node height range (node half-height = 0.5, so ±0.25 is safely inside the boundary).

**AD-02 canonical values** (target = `click` at y=−8.8):
```latex
\coordinate (cleft_up) at (0, -8.55);
\coordinate (cleft_dn) at (0, -9.05);
\draw[->] (err1.west) -- (0.8, -11.5) -- (0.8, -8.55) -- (click.west |- cleft_up);
\draw[->] (err2.west) -- (0.5, -16.5) -- (0.5, -9.05) -- (click.west |- cleft_dn);
```

---

## Decision Label Placement

**CRITICAL — labels must stay near the diamond, not at the arrow midpoint.**
Midpoint of a cross-lane arrow lands on the vertical divider (x=5.8) and overlaps it.

```latex
%% F label — pos=0.08 keeps it within the Sistem lane, just right of diamond west tip
\draw[->] (d.west) -- node[above, pos=0.08, font=\scriptsize] {F} (err.east);

%% T label — pos=0.12 keeps it just below the diamond south tip
\draw[->] (d.south) -- node[right, pos=0.12, font=\scriptsize] {T} (next.north);
```

| Label | Exit | `pos` | Anchor | Result |
|-------|------|--------|--------|--------|
| F | `d.west` | `0.08` | `above` | ~0.09cm right of diamond tip, inside Sistem lane |
| T | `d.south` | `0.12` | `right` | ~0.31cm below diamond tip |

---

## Y-Spacing Rules

Target: **~2.5 units centre-to-centre** between consecutive action nodes or action↔diamond.
Minimum gap between **node edges**: 1.0 unit (= 1cm after scalebox removed).

Half-heights for edge calculations:
- Action node: `0.5cm` (minimum height 1.0cm)
- Decision diamond: `0.65cm` (height ≈ 1.3cm with aspect=1.6, text width=2.0cm)

Reference y-positions from AD-02 Login (use as template):

| Element | y | Gap to next |
|---------|---|-------------|
| Start circle | −1.8 | — |
| access / showform | −3.5 | 1.7 (start→node) |
| bridge bend | −5.0 | 1.1 (node bottom→bend) |
| fill | −6.5 | 1.0 (bend→node) |
| click / norm | −8.8 | 2.3 (centre-to-centre) |
| d1 / err1 | −11.5 | 2.7 ← **extra** for norm's text overhang |
| verify | −14.0 | 2.5 |
| d2 / err2 | −16.5 | 2.5 |
| session | −19.2 | 2.7 |
| redir | −21.5 | 2.3 |
| end circle | −23.2 | 1.7 (node bottom→circle top) |
| border bottom | −24.5 | 1.0 margin |

**Extra gap rule:** Add 0.5 extra units after any node whose text is longer than average (3+ words per line or a `\texttt{}` token) to prevent text overhang from colliding with the next node.

---

## X-Position Reference

| Object | x |
|--------|---|
| Canvas left | 0 |
| EPC lane centre (nodes) | 2.8 |
| EPC lane label | 2.9 |
| Loop-back gutter 1 (closest error) | 0.8 |
| Loop-back gutter 2 (deeper error) | 0.5 |
| Vertical divider | 5.8 |
| Sistem lane centre (nodes) | 9.0 |
| Sistem lane label | 8.9 |
| Canvas right | 12 |

---

## Figure Wrapper Template

```latex
\begin{figure}[H]
\centering
\scalebox{0.65}{%
\begin{tikzpicture}[
  font=\small,
  action/.style={
    rectangle, rounded corners=4pt, draw=black, thick,
    text width=3.5cm, align=center, minimum height=1.0cm, fill=white
  },
  decision/.style={
    diamond, draw=black, thick, aspect=1.6,
    text width=2.0cm, align=center, fill=white
  },
  >=latex,
]
  %% ... diagram content ...
\end{tikzpicture}%
}
\caption{Activity Diagram <UseCaseName>}
\label{fig:ad<NN>-<slug>}
\end{figure}
```

Label convention: `fig:ad<two-digit-number>-<kebab-slug>` (e.g., `fig:ad07-trigger`).

---

## Generation Checklist

Before writing TikZ for a new activity diagram:

1. **Canvas bottom**: compute `last_node_y - 1.5`; set border to `last_node_y - 1.3`.
2. **Divider start**: always `(5.8, -0.1)` — never `(5.8, 0.7)`.
3. **Node text**: use explicit `\\` for every line break; max 2 lines per node.
4. **Decision exits**: west = F (→ error in EPC lane), south = T (→ continue down).
5. **F label**: `pos=0.08, above, font=\scriptsize` — stays inside Sistem lane.
6. **T label**: `pos=0.12, right, font=\scriptsize` — stays near diamond tip.
7. **Loop-back**: first error gutter x=0.8, second x=0.5; define `\coordinate (cleft_up)` and `(cleft_dn)` at `target_y ± 0.25`; target with `(node.west |- cleft_up/dn)` — do NOT use hardcoded left-edge x.
8. **End node**: outer circle 0.28cm, inner 0.15cm; arrow tip at `end_y + 0.28`.
9. **Scalebox**: always `0.65` — do not use `\resizebox`.
10. **Caption/label**: follow `Activity Diagram <Name>` / `fig:adNN-slug` convention.

---

## Common Mistakes to Avoid

| Mistake | Symptom | Fix |
|---------|---------|-----|
| Divider at `(5.8, 0.7)` | Title text is cut by vertical line | Change to `(5.8, -0.1)` |
| `text width=3.0cm` | "Normalisasi username; cari user di database" wraps to 4 lines, overflows into next node | Use `3.5cm` |
| F/T label at default `pos=0.5` | Labels land on or near the divider line | Use `pos=0.08` (F) and `pos=0.12` (T) |
| Both loop-back arrows at same gutter x | Arrows overlap in left gutter | Use x=0.8 for first, x=0.5 for second || Loop-back ends at hardcoded x (e.g. `1.05`) | Arrowhead enters node interior — inner sep not accounted for | Use `(node.west \|- helper_coord)` to derive exact left edge |
| L-shaped return split into two `\draw[->]` | Unwanted arrowhead appears at the bend corner | Use single `\draw[->] (A.south) -- bend -- (B.north)` |
| Vertical spacing < 2.0 units | Nodes and diamonds overlap after text expansion | Use 2.5 units minimum; 2.7 after tall nodes |
| Missing `fill=white` on nodes | Nodes do not cover arrow lines behind them | Always include `fill=white` in both styles |
