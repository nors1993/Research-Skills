# 图5机制/框架图设计蓝图

## 设计原则

图5通常是论文的架构性/机制性图表，是全文最核心的视觉论据。用户对图5质量敏感度最高，**简单箭头+方框会被退回**。

**生成方式优先级**：matplotlib 适合数据图表（折线、柱状、散点）；对于框架/机制/架构图，优先使用 **SVG 直写**（参见 `references/fireworks-tech-graph-figures.md`），产出更专业、文本不会与图形重叠。

## 推荐方案 A：SVG 直写（fireworks-tech-graph 风格）⭐

适用于框架图、架构图、产业链图。参见 `references/fireworks-tech-graph-figures.md` 获取完整工作流。

关键要点：
- 双面板用 1400×800+ viewBox，虚线分隔
- 使用 Style 1 (Flat Icon) 颜色系统
- 节点用 `<rect rx="10">` + `<filter>` 阴影
- 连接线用 `<line>` + `<marker>` 箭头
- 图例放底部，5列水平排列
- **&nbsp; 实体在 SVG 中非法，必须用 `&#160;`**
- 用 `validate-svg.sh` 验证，cairosvg 导出 PNG

## 推荐方案 B：Matplotlib（数据类图表）

## 推荐双面板设计（A vs B 对比）

对于有时序演变（如"2020 vs 2026+"）的机制图，使用 `matplotlib` 双面板布局：

```python
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(14, 6))
```

### 左面板（旧范式）：传统流程

- 使用 `FancyBboxPatch` 绘制圆角矩形链
- 箭头连接各环节
- 灰色调，强调"过去"
- 底部列出关键特征列表（`• 政府主导` 等）

### 右面板（新范式）：生态系统

- 使用 `Circle` 绘制中心枢纽节点（大圆）
- 使用小 `Circle` 绘制生态节点，环绕中心
- 不同颜色区分功能类别（蓝=通信、绿=遥感、紫=导航、橙=IoT）
- 连接线从节点到中心（`ax.plot`，带透明度）
- 底部列出关键使能因素
- 添加图例（`ax.legend`）

## 代码模板关键函数

```python
from matplotlib.patches import FancyBboxPatch, Circle

# 圆角矩形
rect = FancyBboxPatch((x-1, y-0.6), 2, 1.2, boxstyle="round,pad=0.1",
                       linewidth=1.5, edgecolor='#999', facecolor='#EEE', alpha=0.9)
ax.add_patch(rect)

# 圆形节点
hub = Circle((6, 5), 1.8, linewidth=3, edgecolor='#1565C0', facecolor='#E3F2FD', alpha=0.95)
ax.add_patch(hub)

# 连接线
ax.plot([node_x, hub_x], [node_y, hub_y], '-', color=color, linewidth=1, alpha=0.3)
```

## 常见退回原因

| 问题 | 症状 | 修复 |
|------|------|------|
| 内容缺失 | 仅2-3个节点，看不出生态 | 确保右面板至少8个生态节点 |
| 无对比维度 | 只有一张图，看不出变化 | 使用双面板 A vs B |
| 无图例 | 颜色含义不明 | 添加 `ax.legend()` |
| 节点无区分 | 所有节点同色同形 | 按功能类别使用不同颜色 |
| 无数据说明 | 图源不明 | 底部添加 `Source: Author analysis based on...` |
| **中文标注** | 图中出现中文导致字体缺失/乱码 | **所有图表标注一律用英文**，论文正文为中文但图表文本全英文化 |
| 文字与图形重叠 | matplotlib 坐标与文本冲突 | 改用 SVG 直写方案，精确控制布局 |
| 中文标注 | 使用中文导致字体缺失（`Glyph missing from DejaVu Sans`）| **所有图表文字必须用英文**。用户明确要求"图片中不需要有中文"。DejaVu Sans 无 CJK 字形，即使安装中文字体也易出现渲染问题 |
| 审美粗糙 | "很丑，布局不合理" | 使用专业配色（Tailwind/Tableau 色板如 `#1A56DB` `#047857` `#7C3AED` `#D97706` `#DC2626`）；节点加白色填充+彩色边框提高可读性；添加 subtle 背景引导圆（虚线同心圆）；贝塞尔曲线替代直线连接；节点间适当留白 |

## 设计美学准则

1. **尺寸**: 至少 18×9 inches，dpi≥200，保证高分辨率
2. **配色**: 使用成熟色板（Tailwind、Material Design、Tableau），避免饱和度过高的纯色
3. **面板背景**: 双面板各自用极淡灰 `#FAFAFA` 背景矩形区分
4. **节点样式**: 白色填充 + 2-2.5pt 彩色边框 + 文字用边框色，比纯色填充专业得多
5. **连接线**: 使用二次贝塞尔曲线（`np.linspace` 插值），alpha=0.25-0.3，避免硬直线
6. **引导元素**: 右面板加虚线同心圆（`edgecolor='#E2E8F0'`）作为视觉锚点
7. **标题**: `A  Description (Year)` 格式，14pt bold，深灰 `#475569`
8. **图源**: 底部 `Source: ...` 7.5pt italic，浅灰 `#94A3B8`
