---
name: paper-writing
description: "Write research paper for any academic domain following specified templates."
---

## 文献计量/科学计量类论文的特殊处理

如果用户提交的作业/论文模板涉及 **"bibliometric analysis"、"文献计量"、"bibliometric"、"scientometric"、"主题演化"、"keyword co-occurrence"、"VOSviewer"、"Bibliometrix"** 等关键词，则该论文属于**文献计量类**（非常规议论文）。其撰写流程与常规论文有根本性差异——核心论据来自对真实文献数据的结构化分析，而非逻辑推演。

此时，**不要盲目执行 Step 1-7 的标准流程**。先读取 `references/bibliometric-paper-workflow.md` 获取完整的调整后工作流。该工作流的关键调整：

1. **Step 1 不再是可行性调研，而是真实数据收集**——使用 CrossRef API 作为首选检索工具（Semantic Scholar 大概率限速或返回空）
2. **Step 2 是计量分析 + 可视化生成**——从 JSON 数据提取统计量，生成图表
3. **源数据必须导出为 CSV/Excel**——检索式可验证，数据可复现
4. **Discussion 必须紧扣数据**——不能在没有数据支撑的情况下断言趋势
5. **明确承认数据局限**——如早期年份论文过少，在论文中说明而非掩盖

核心注意事项：检索式必须精确可验证（课程作业中老师会重新运行），源数据必须提交，Discussion 需基于独立学术分析而非 AI 生成。

---

## 学术水平调整（Tone Level Adjustment）

用户可能要求将已完成的论文从某个水平改写至另一个水平（如"改写成本科四年级学生的水平""改成硕士水平""现在显得太专家了"等）。此时：

1. **不要从头重写**——保留数据、结构、参考文献和分析结果不变
2. **仅调整语言层面**：
   - **降级**（专家→本科）：缩短句子、替换学术黑话为平实表达、减少被动语态、增加说明性语言
   - **升级**（本科→专家）：使用更精确的术语、复合句式、批判性措辞
3. **同步更新关联文档**：可行性评估报告和文献综述也应同步调整语言水平
4. **字数自然变化**：降级通常会使字数减少（~6000→~4000），这是预期行为，无需强行补足
5. 不要改变检索式、数据表格、统计数字、图表和参考文献
6. 详见 `references/undergraduate-level-adjustment.md` 获取完整的降级改动清单

# 必须遵守
1. 如果用户要求重新编写，或者重新研究，需要忘记历史内容，重新开始。
2. 如果用户要求"改写"已有论文（非重写），保留数据和结构，仅调整语言层。
2. 任务开始前，先让用户提供文件保存的目录位置，后续所有文件都保存在用户提供的目录中。
3. 让用户指定撰写文章所用的语言：中文、英语、法语、日语等。

# 禁止
下述step 1 ~ step 7，任何一个step未完成，不允许进入下一个step 。

# Workflow
请严格按照以下步骤为用户撰写论文文档：
列出所有可用的
1. **Step 1: 意图理解与可行性调研 (Feasibility Study)**
   - 接收用户的初步想法，将用户的输入内容进行UTF-8格式的转换，防止输入意图有乱码。
   - 思考：该方向是否已被广泛研究？有无极度相似的现成论文或专利？
   - 动作：直接执行可行性调研（子技能 research-idea-parser / research-feasibility-researcher 可能不可用，agent 自行完成即可）。使用 arXiv API、Semantic Scholar API、浏览器检索真实文献。调用技能**docx**向用户出具可行性评估报告《XXX可行性评估报告.docx》（包含已识别文献列表、相似工作、创新点挖掘建议）。若想法不可行，需引导用户调整方向。
   - 必须：用户确认可行后再继续下一步。

2. **Step 2: 深度调研与资料收集 (Deep Research)**
   - 确定研究方向后，进行全面检索。
   - 动作：直接执行深度调研（子技能 research-deep-researcher 可能不可用，agent 自行完成）。至少15篇文献，附原文链接。文献必须真实存在，不能伪造，必须含**DOI**验证。
   - **检索策略优先级**：① CrossRef API `/works?query=...`（首选——无限速，对多词英文查询容忍度高）；② Semantic Scholar API（备选——使用 `urllib.parse.urlencode` 编码，每两次请求间隔 ≥ 1.5s，被限速后换 Crossref）；③ arXiv API（备选——仅适用于 STEM 技术类论文）。搜索引擎如 Google Scholar 可能对数据中心 IP 返回 CAPTCHA，**不要作为首选**。
   - 详见 `references/api-search-pitfalls.md` 以获取完整的 API 检索坑点和应对方案。
   - **如果搜索文献少于15篇，直接退出任务！并告知用户退出任务原因。**
   - 产出：必须调用技能**docx**生成详细的文献综述，命名规则为《文献综述与资源列表.docx》。
   - 必须：用户确认可行后再继续下一步。

3. **Step 3: 核心观点与大纲起草 (Drafting)**
   - 基于调研结果，构建论文的核心论点与创新架构。
   - 动作：基于调研结果构建论文的核心论点与创新架构。默认参考目录 **skills\\paper-writing\\references** 内文档的格式，直接起草文档（子技能 research-paper-drafting 可能不可用）。如果用户提供了文件模板要求，则根据用户提供的模板进行撰写。**文档章节和格式必须与模板文件一样！**
   - 产出：《XXX.docx》。

4. **Step 4: 逻辑自洽与推演校验 (Logic Validation)**
   - 动作：自行扮演"苛刻的审稿人（Reviewer）"角色（子技能 research-consistency-checker 可能不可用），对生成的论文进行交叉验证：检查论点之间是否存在逻辑矛盾、数据是否前后一致、论证链是否完整。

5. **Step 5: 查重与重复率控制 (Plagiarism Check)**
   - 动作：自行对文中关键段落进行查重检测（子技能 research-plagiarism-detector 可能不可用），对比互联网与已公开论文，确保原创性。

6. **Step 6: 语言润色与去AI化 (Polishing & Humanizing)**
   - 动作：自行对全文进行最终语言审查（子技能 research-style-humanizer 可能不可用），消除"AI生成"痕迹，使用精炼、客观、严谨的学术语言，拒绝典型 AI 词汇（"总而言之""深入探讨""不难发现""画卷"等）。

7. **Step 7: 生成最终的文档 (Publishing)**
   - 动作：确保《XXX可行性评估报告.docx》、《文献综述与资源列表.docx》和最终版论文《XXX.docx》三个文件均已生成，显性通知用户任务已完成，并给出最终文档保存的位置。如果在文档保存的目录中生成了过程中的代码文件，比如.py、.mjs、.js、.ts等格式文件，需要将其删除。