---
name: paper-writing
description: "Write research paper for any academic domain following specified templates."
---
# Workflow
请严格按照以下步骤为用户撰写论文文档：

1. **Step 1: 意图理解与可行性调研 (Feasibility Study)**
   - 接收用户的初步想法。
   - 思考：该方向是否已被广泛研究？有无极度相似的现成论文或专利？
   - 动作：调用技能research-idea-parser和research-feasibility-researcher进行初步验证，并调用技能docx并向用户出具一份简短的可行性评估报告，命名为《XXX可行性评估报告.docx》（包含相似工作、创新点挖掘建议）。若想法不可行，需引导用户调整方向。
   - 必须：用户确认可行后再继续下一步。

2. **Step 2: 深度调研与资料收集 (Deep Research)**
   - 确定研究方向后，进行全面检索。
   - 动作：调用技能research-deep-researcher进行调研，至少15篇文献。**如果搜索文献少于15篇，直接退出任务！并告知用户退出任务原因。**
   - 产出：必须调用技能docx生成详细的文献综述，命名规则为《文献综述与资源列表.docx》。
   - 必须：用户确认可行后再继续下一步。

3. **Step 3: 核心观点与大纲起草 (Drafting)**
   - 基于调研结果，构建论文的核心论点与创新架构。
   - 动作：调用技能research-paper-drafting撰写文档，模板从目录 skills\research\research-flow\paper\references\ 动态加载（支持多个模板文件）。**文档章节和格式必须与模板文件一样！**
   - 产出：《XXX.docx》。

4. **Step 4: 逻辑自洽与推演校验 (Logic Validation)**
   - 动作：调用技能research-consistency-checker扮演“苛刻的审稿人（Reviewer）”角色，对生成的论文进行交叉验证。

5. **Step 5: 查重与重复率控制 (Plagiarism Check)**
   - 动作：调用技能research-plagiarism-detector对文中的关键段落进行查重检测，对比互联网与已公开论文。

6. **Step 6: 语言润色与去AI化 (Polishing & Humanizing)**
   - 动作：调用技能research-style-humanizer对全文进行最终语言审查。

7. **Step 7: 生成最终的文档 (Publishing)**
   - 动作：显性通知用户任务已完成，并给出最终文档保存的位置。如果在文档保存的目录中生成了过程中的代码文件，比如*.py  *.mjs  *.js  *.ts等，需要将其删除。

# 禁止
上述step 1 ~ step 7，任何一个step未完成，不允许进入下一个step 。