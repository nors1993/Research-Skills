---
name: patent-writing
description: "Write patent for any domain following specified templates."
---
# 必须遵守
1. 如果用户要求重新编写，或者重新研究，需要忘记历史内容，重新开始。
2. 任务开始前，先让用户提供文件保存的目录位置，后续所有文件都保存在用户提供的目录中。
3. 让用户指定撰写文章所用的语言：中文、英语、法语、日语等。

# 禁止
下述step 1 ~ step 7，任何一个step未完成，不允许进入下一个step 。

# Workflow
请严格按照以下步骤为用户撰写专利文档：

1. **Step 1: 意图理解与可行性调研 (Feasibility Study)**
   - 接收用户的初步想法，将用户的输入内容进行UTF-8格式的转换，防止输入意图有乱码。
   - 思考：该方向是否已被广泛研究？有无极度相似的现成论文或专利？
   - 动作：直接执行可行性调研。参考 `references/patent-drafting.md` 中 claim-first 起草逻辑，以及 paper-writing 技能 references/ 目录下的 idea-parser.md（结构化解析策略）和 feasibility-researcher.md（领域特定检索策略）。调用技能**docx**向用户出具一份简短的可行性评估报告，命名为《XXX可行性评估报告.docx》（包含相似工作、创新点挖掘建议）。若想法不可行，需引导用户调整方向。
   - 必须：用户确认可行后再继续下一步。

2. **Step 2: 深度调研与资料收集 (Deep Research)**
   - 确定研究方向后，进行全面检索。
   - 动作：直接执行深度调研。参考 paper-writing 技能 references/deep-researcher.md 中的迭代搜索策略和 API 优先级。至少5篇文献，附上原文链接。文献必须真实存在，不能伪造。**为了防止被api限制访问，搜索过程可以模仿人类搜索**，
   - 产出：必须调用技能**docx**生成详细的文献综述，命名规则为《文献综述与资源列表.docx》。
   - 必须：用户确认可行后再继续下一步。

3. **Step 3: 核心观点与大纲起草 (Drafting)**
   - 基于调研结果，构建论文的核心论点与创新架构。
   - 动作：默认参考目录 **references/** 内文档的格式，参考 `references/patent-drafting.md` 中的 CNIPA 标准 claim-first 撰写流程、多实施例策略和参数范围要求。如果用户提供了文件模板要求，则根据用户提供的模板进行撰写。**文档章节和格式必须与模板文件一样！**
   - 产出：《XXX专利说明书.docx》。

4. **Step 4: 逻辑自洽与推演校验 (Logic Validation)**
   - 动作：自行扮演"苛刻的审稿人（Reviewer）"角色，对生成的专利文档进行交叉验证。详细策略见 paper-writing 技能 references/consistency-checker.md（多视角审稿模式、问题分类、claim-evidence 映射）。

5. **Step 5: 查重与重复率控制 (Plagiarism Check)**
   - 动作：自行对文中关键段落进行查重检测。详细方法见 paper-writing 技能 references/plagiarism-detector.md（段落级相似度计算、分节阈值、重写建议生成），对比互联网与已公开论文。

6. **Step 6: 语言润色与去AI化 (Polishing & Humanizing)**
   - 动作：自行对全文进行最终语言审查。详细方法见 paper-writing 技能 references/style-humanizer.md（AI 模式检测、多轮降痕 Pipeline、中英文 AI 标志词汇清单）。

7. **Step 7: 生成最终的文档 (Publishing)**
   - 动作：确保《XXX可行性评估报告.docx》、《文献综述与资源列表.docx》和《XXX专利说明书.docx》三个文件均已生成，显性通知用户任务已完成，并给出最终文档保存的位置。如果在文档保存的目录中生成了过程中的代码文件，比如.py、.mjs、.js、.ts等格式文件，需要将其删除。