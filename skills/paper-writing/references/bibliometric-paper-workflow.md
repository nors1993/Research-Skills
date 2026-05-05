# 文献计量类论文写作工作流

## 与常规论文的根本差异

文献计量/科学计量论文（bibliometric/scientometric analysis）不是常规议论文——它的核心论据来自**对真实文献数据的结构化分析**，而非逻辑推演。这决定了整个撰写流程与常规论文写作截然不同：

| 维度 | 常规论文 | 文献计量论文 |
|------|---------|------------|
| 第 1 步 | 可行性调研（文献阅读） | **真实数据收集**（API 检索） |
| 第 2 步 | 文献综述 | **计量分析**（统计+可视化） |
| 第 3 步 | 起草大纲 | 基于数据分析结果**反向构建论点** |
| 数据要求 | 引用即可 | 数据集**必须可复现**（检索式+源数据 Excel） |
| 图表 | 可选装饰 | **强制**——趋势图/网络图/主题图不可少 |
| Discussion | 自由推理 | **必须紧扣数据**，不能凭空发挥 |

## 调整后的 Step 序列

### Step 1: 数据收集 (Data Collection)
- 使用 **CrossRef API** 作为首选检索工具（Semantic Scholar 大概率限速或返回空）
- 用多个查询组合覆盖主题的不同侧面（如 `"urban food security"`, `"urban food system" climate`, `"urban agriculture" "food security"`）
- 去重键：DOI
- 年份过滤：Python 侧 `if y and 2000 <= y <= 2025`
- 保存中间 JSON，防止超时丢失
- **目标**：收集 200+ 篇论文，确保三个时间切片的论文数量足够分析

### Step 2: 计量分析 (Bibliometric Analysis)
- 从 JSON 数据中提取：
  - 年度发文量（`Counter(p['year'])`）
  - 期刊排名（`Counter(p['journal'])`）
  - 作者排名（`Counter(author)`）
  - 关键词分类统计（按主题类别如 climate/access/systems 分别统计）
- 时间切片：2000-2010 / 2011-2019 / 2020-2025
- 每一切片独立分析

### Step 3: 可视化生成 (Figure Generation)
- 优先尝试 `matplotlib`（`pip install matplotlib --break-system-packages`）
- 若 matplotlib 安装失败/超时：用**纯 Python 生成 SVG**（stdlib 即可，见下方模式）
- 图表类型：趋势折线/柱状图、期刊排名水平柱状图、主题演化对比柱状图

### Step 4: 论文撰写 (Writing)
- **Introduction** → 基于收集到的高引文献构建背景和 research gap
- **Literature Review** → 基于实际检索到的核心论文来 synthesize trends（不要凭空列举不存在的文献）
- **Methodology** → 必须包含**精确可验证的检索式**（布尔表达式，可直接在 WoS/Scopus 中执行）
- **Results** → 所有数字来自真实数据，表格和图表位置明确标注
- **Discussion** → 紧扣数据发现，区分"数据支持"与"数据不支持"的结论。**绝对禁止**：在没有数据支撑的情况下断言趋势
- **Conclusion** → 总结计量发现，指出数据缺口（如"2000-2010 仅有 4 篇论文，早期趋势不可靠"）

### Step 5: 源数据导出 (Source Data Export)
- CSV 格式（`utf-8-sig` 编码，Excel 兼容）
- 列：Title, Authors, Year, Journal, DOI, Cited By, Abstract
- 额外导出 summary_stats.csv（年度统计、期刊排名、作者排名）
- **这是课程作业的关键交付物**——源数据将被验证

## 纯 Python SVG 图表生成模式

当 matplotlib 不可用时，用纯 Python stdlib 生成 SVG 柱状图：

```python
def svg_bar_chart(data, title, xlabel, ylabel, width=800, height=400, filename='chart.svg'):
    """data = [(label, value), ...]"""
    labels, values = zip(*data)
    n = len(labels)
    bar_w = (width - 120) / n * 0.7
    max_v = max(values)
    chart_h = height - 100
    
    svg = [f'<svg xmlns="http://www.w3.org/2000/svg" width="{width}" height="{height}">']
    svg.append(f'<rect width="{width}" height="{height}" fill="white"/>')
    svg.append(f'<text x="{width/2}" y="30" text-anchor="middle" font-size="16" font-weight="bold">{title}</text>')
    
    # Axes
    svg.append(f'<line x1="80" y1="40" x2="80" y2="{40+chart_h}" stroke="black" stroke-width="2"/>')
    svg.append(f'<line x1="80" y1="{40+chart_h}" x2="{width-40}" y2="{40+chart_h}" stroke="black" stroke-width="2"/>')
    
    # Bars
    colors = ['#2E75B6', '#E74C3C', '#2ECC71', '#F39C12', '#9B59B6', '#1ABC9C', '#E67E22', '#3498DB']
    for i, (label, val) in enumerate(zip(labels, values)):
        x = 80 + i * (bar_w + gap)
        bar_h = (val / max_v) * chart_h if max_v > 0 else 0
        y = 40 + chart_h - bar_h
        svg.append(f'<rect x="{x:.0f}" y="{y:.0f}" width="{bar_w:.0f}" height="{bar_h:.0f}" fill="{colors[i%8]}" rx="2"/>')
        svg.append(f'<text x="{x+bar_w/2:.0f}" y="{40+chart_h+15}" text-anchor="end" font-size="9" transform="rotate(-45, {x+bar_w/2:.0f}, {40+chart_h+15})">{label[:15]}</text>')
    
    svg.append('</svg>')
    with open(filename, 'w') as f:
        f.write('\n'.join(svg))
```

SVG 可在浏览器中直接预览，也可用 ImageMagick/rst2pdf 等转换为 PNG/PDF。

## 注意事项

1. **检索式必须可验证**：课程作业中，老师会重新运行检索式验证数据集。检索式必须包含确切的关键词、布尔运算符、数据库名称和时间范围。
2. **源数据必须提交**：CSV/Excel 格式，包含完整元数据。
3. **Discussion 不能 AI 生成**：课程作业通常明确禁止 AI 直接生成 Discussion。应基于真实数据分析结果，用独立学术语言撰写，紧扣数据发现而非泛泛而谈。
4. **时间切片必须有理由**：不要随意分段时间，用外部事件（COVID-19、IPCC 报告、SDG 采用）作为切片依据。
5. **承认数据局限**：如早期年份论文过少（如 2000-2010 仅 4 篇），在论文中明确说明这一局限，不要掩盖。
