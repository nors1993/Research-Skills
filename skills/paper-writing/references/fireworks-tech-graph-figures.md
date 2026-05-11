# Fireworks-Tech-Graph 图表生成方案

## 何时使用

当 matplotlib 生成的图表被用户反馈"丑""布局不合理""文字重叠"时，改用 SVG 直写方式。fireworks-tech-graph 技能提供了风格化的 SVG 模板和颜色系统，产出更专业。

## 安装与使用

```bash
# 克隆仓库（npx skills add 交互式，不适用 Hermes）
git clone https://github.com/yizhiyanhua-ai/fireworks-tech-graph.git /tmp/fireworks-tech-graph

# 参考风格文件获取颜色/字体规范
cat /tmp/fireworks-tech-graph/references/style-1-flat-icon.md

# 验证 SVG 语法
bash /tmp/fireworks-tech-graph/scripts/validate-svg.sh output.svg
```

## SVG → PNG 转换

```bash
# 优先: rsvg-convert（需 librsvg2-bin，常不可用）
rsvg-convert -w 2800 input.svg -o output.png

# 备选: cairosvg（pip install cairosvg --break-system-packages）
python3 -c "
import cairosvg
cairosvg.svg2png(url='input.svg', write_to='output.png', output_width=2800, output_height=1600)
"
```

## 关键坑点

### &nbsp; 实体在 SVG 中非法
SVG 是 XML，不接受 HTML 实体 `&nbsp;`。必须使用：
- `&#160;` （数字字符引用） ✅
- 或直接用普通空格 ✅
- `&nbsp;` ❌ → 报错 `xml.etree.ElementTree.ParseError: undefined entity`

### 字体族声明
使用 skill style-1 推荐的字体栈，覆盖中英文：
```css
font-family: 'Helvetica Neue', Helvetica, Arial, 'PingFang SC', 'Microsoft YaHei', sans-serif;
```

### 验证脚本
即使 rsvg-convert 不可用，`validate-svg.sh` 也能检查标签平衡、属性引号、marker 引用等。

## Style 1 (Flat Icon) 颜色速查

```
Background:    #ffffff
Box fill:      #ffffff
Box stroke:    #d1d5db
Text primary:  #111827
Text secondary:#6b7280

Semantic colors:
  Blue:   #2563eb  (通信/主流程)
  Red:    #dc2626  (基础设施/警告)
  Green:  #16a34a  (遥感/数据)
  Purple: #9333ea  (导航/异步)
  Orange: #f97316  (IoT/新兴)
```

## 双面板对比图（如产业链重构）布局要点

1. ViewBox 宽度 ≥ 1400（每面板 700+），高度 800+
2. 两面板用虚线分隔（`stroke-dasharray="4 4"`）
3. 左侧线性链条：节点居中排列，箭头连接，特征列表放面板边缘
4. 右侧生态图：中央大圆为 hub，8 个小圆均匀环布，半透明连接线
5. 图例放底部中央，5 列水平排列，避免遮挡节点
6. 源注释放最底部，斜体灰色小字
