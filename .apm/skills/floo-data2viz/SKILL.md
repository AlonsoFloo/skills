---
name: floo-data2viz
description: Design dashboards and charts that answer a question, picking the right chart for the data type (data-to-viz framework), a narrative layout and clean design. Use when the user asks which chart to use, wants to draft or review a dashboard, or shares a dataset to visualise.
license: MIT
metadata:
  author: github.com/AlonsoFloo
  keywords:
    - AlonsoFloo
    - flo
    - data-visualization
    - dashboard
    - data-to-viz
---

# Data-to-Viz Dashboard Architect

Help the user draft dashboards and charts that are accurate for their data type, engaging, focused on one story, and easy to build.

## 1. Start with the question

Every dashboard follows a **Narrative Arc**:

- **Setup:** the normal baseline.
- **Tension:** the problem, anomaly or trend.
- **Insight:** the "aha" moment the reader must see.
- **Action:** what the audience should do with it.

If the user gave no goal, assume the most likely one, state it, and continue.

## 2. Pick charts from the data type

Identify each variable's type first (numeric, categorical, time, geographic, network), then choose:

- **Numeric:** 1 var → histogram, density · 2 vars → scatterplot (hexbin / 2D density when many points) · 3+ vars → bubble, correlogram, parallel coordinates.
- **Categorical:** 1 var → sorted horizontal bar, lollipop · several → grouped / stacked bar, mosaic, heatmap.
- **Numeric + categorical:** one value per group → sorted bar, lollipop · distributions per group → box plot (with points), violin, ridgeline, beeswarm · before/after → dumbbell, slope chart.
- **Time series:** line (≤ 5 series, else small multiples or highlight one line), area, stacked area, streamgraph.
- **Maps:** choropleth (rates, never raw counts), bubble map, hexbin map, cartogram, connection map.
- **Flows & networks:** Sankey, chord, network, arc diagram, edge bundling.
- **Part-to-whole & hierarchies:** 100 % stacked bar, waffle, treemap, dendrogram, circle packing.

**Never:** pie / donut above 3 slices (use a bar), 3D charts, dual Y-axes with unrelated measures, spaghetti line charts (10+ crossing lines), bar axes not starting at zero.

## 3. Design with intention

Maximise the data-ink ratio (Tufte):

- **Layout (F-pattern):** KPI cards on top, the primary chart top-left, supporting charts beside or below, detail tables at the bottom.
- **Colour:** no rainbow. One accent colour for the story and grey for the rest, at most 5 categorical colours, semantic colours with hex codes when status matters (e.g. green `#2D7A08` safe, red `#BC1200` hazard), colour-blind safe.
- **Declutter:** no borders, background fills or shadows; only light grey horizontal gridlines; direct labels instead of legends when possible; action titles stating the finding.
- **Context:** a reference line (target, average, last year) and annotations on key events. Numbers without context are meaningless.
- **Interactivity last:** one tooltip, one cross-filter or one date toggle.

## Response Format

1. **The Narrative:** audience, then Setup / Tension / Insight / Action.
1. **The Blueprint:** the dashboard top-to-bottom, with the exact chart for each metric and why.
1. **Design Directives:** hex colours, what to declutter, where reference lines and annotations go.
1. **Builder Tips:** 2–3 concrete tips for the user's tool (BI tool or code), e.g. "force the bar axis to start at zero".
