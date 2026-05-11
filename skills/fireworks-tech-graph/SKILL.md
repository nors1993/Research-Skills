---
name: fireworks-tech-graph
description: >-
  Generate production-quality technical diagrams (architecture, comparison, flow) 
  as SVG+PNG using the fireworks-tech-graph style system. Trigger on: "画架构图"
  "画对比图" "技术图表" "generate diagram" "system diagram" "comparison diagram".
---

# Fireworks Tech Graph — Dual-Panel & Architecture Diagrams

Install source: `git clone https://github.com/yizhiyanhua-ai/fireworks-tech-graph.git`
Style reference: `references/style-1-flat-icon.md` (Flat Icon, default)
Converter: `cairosvg` (pip install cairosvg --break-system-packages)
Validation: `scripts/validate-svg.sh` from the repo

## Workflow

1. Classify diagram type (architecture, comparison, data-flow, flowchart…)
2. Design layout in a generous viewBox (min 1600×900 for dual-panel)
3. Write clean SVG using the style-1 color palette
4. Convert: `python3 -c "import cairosvg; cairosvg.svg2png(url='in.svg', write_to='out.png', output_width=2800, output_height=1575)"`
5. Validate: `bash scripts/validate-svg.sh file.svg`

## Critical Layout Rules (anti-overlap)

1. **ViewBox first**: don't cram — 1600×900 minimum for dual-panel
2. **Circle nodes**: r ≥ 54px when text is ≤18 chars at font-size 9; font-size ≤ 9 inside circles
3. **Text-in-circle check**: longest label × 5.5px ≤ circle diameter (108px for r=54)
4. **Node spacing**: adjacent circles 45° apart on r=300 → 229px center-to-center (safe)
5. **Legend positioning**: y ≥ bottom-node-edge + 26px
6. **Never use `&nbsp;`** in SVG — use `&#160;` (valid XML entity)
7. **Margin zones**: 60px gutter between left-panel nodes and right-side annotations
8. **Rightmost nodes**: x + r ≤ viewBox width - 26px

## Style-1 Color Palette (Flat Icon)

```
Background:     #ffffff
Box fill:       #ffffff → #F1F5F9 (light tiers)
Box stroke:     #94A3B8 / #d1d5db
Text primary:   #111827 / #334155
Text secondary: #64748B / #6b7280
Blue accent:    #1A56DB (communication)
Green accent:   #047857 (earth observation)
Purple accent:  #7C3AED (navigation)
Orange accent:  #D97706 (IoT)
Red accent:     #DC2626 (infrastructure)
Hub fill:       #DBEAFE → #EFF6F6 (radial gradient)
Shadow filter:  feDropShadow dy=2 stdDeviation=4 #00000018
```

## SVG Patterns

### Dual-panel comparison (Value Chain / Ecosystem)
```xml
<svg viewBox="0 0 1600 900">
  <!-- Divider: dashed vertical line at midpoint -->
  <line x1="800" y1="70" x2="800" y2="880" stroke="#E2E8F0" stroke-dasharray="6 6"/>
  
  <!-- Left panel: vertical chain with rounded rects + arrows -->
  <!-- Right panel: hub-and-spoke with circle nodes + radial connections -->
</svg>
```

### Circle node with text
```xml
<circle cx="X" cy="Y" r="54" fill="#FFFFFF" stroke="COLOR" stroke-width="2.2" filter="url(#shadowLight)"/>
<text x="X" y="Y-7" text-anchor="middle" font-size="9" font-weight="700" fill="COLOR">Label Line 1</text>
<text x="X" y="Y+9" text-anchor="middle" font-size="8" fill="#64748B">Sub-label</text>
```

### Legend pattern
```xml
<rect x="L" y="Y" width="W" height="48" rx="8" fill="#F8FAFC" stroke="#E2E8F0"/>
<!-- Color dots + labels, spaced evenly across width -->
```

## Pitfalls

- `&nbsp;` is HTML, not XML — always use `&#160;` in SVG
- `rsvg-convert` may not be installed; `cairosvg` is the reliable Python fallback
- Text inside circles: reduce font-size before enlarging circles (keep proportions)
- Vision API may reject large PNGs; convert at output_width ≤ 2800
- Don't trust visual inspection alone — compute text width vs circle diameter
