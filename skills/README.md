# Research Skills 业务工作流文档 / Business Workflow Documentation

---

## 概述 / Overview

本目录包含一系列专业的学术研究辅助 Skills，用于支持从研究想法到最终文档产出的完整业务流程。这些 Skills 覆盖了 **论文写作** 和 **专利撰写** 两大应用场景。

This directory contains a suite of professional academic research assistant Skills designed to support the complete business process from research ideas to final document delivery. These Skills cover two major application scenarios: **paper writing** and **patent drafting**.

---

## 目录结构 / Directory Structure

```
skills/
├── arxiv/                    # 学术论文预印本搜索
├── blogwatcher/               # 博客内容监控
├── docx/                   # Word 文档处理
├── paper-writing/           # 论文撰写 (主流程)
│   ├── references/        # 参考文档
│   └── asserts/          # 断言模板
├── patent-writing/        # 专利撰写
└── research-*/           # 细分研究技能
    ├── research-intent/              # 意图判断
    ├── research-idea-parser/       # 想法解析
    ├── research-feasibility-researcher/   # 可行性调研
    ├── research-deep-researcher/       # 深度文献调研
    ├── research-paper-drafting/     # 论文起草
    ├── research-patent-drafting/    # 专利起草
    ├── research-consistency-checker/  # 逻辑检查
    ├── research-plagiarism-detector/ # 查重检测
    └── research-style-humanizer/     # 语言润色
```

---

## 业务流程总览 / Business Process Overview

### 论文写作流程 / Paper Writing Process

```
用户输入研究想法
       │
       ▼
┌──────────────────────┐
│ research-intent      │ ← 判断: 论文还是专利?
└──────────────────────┘
       │
       ▼ (论文流程)
┌────────────────────┐    ┌──────────────────────────┐
│ research-idea-     │ →  │ research-feasibility-    │
│ parser            │    │ researcher               │
│ (想法解析)        │    │ (可行性调研)              │
└────────────────────┘    └──────────────────────────┘
         │                           │
         ▼                           ▼
┌──────────────────────────────────────────────┐
│    research-deep-researcher                  │
│    (深度文献调研 - 至少15篇真实文献)            │
└──────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────┐
│    research-paper-drafting                    │
│    (论文撰写)                                 │
└──────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────┐
│    research-consistency-checker              │
│    (逻辑一致性检查)                           │
└──────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────┐
│    research-plagiarism-detector              │
│    (查重检测)                                │
└──────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────┐
│    research-style-humanizer                  │
│    (语言润色 - 去AI化)                       │
└──────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────┐
│    docx                                       │
│    (生成Word文档)                             │
└──────────────────────────────────────────────┘
         │
         ▼
    最终论文交付
```

---

### 专利撰写流程 / Patent Writing Process

```
用户输入发明想法
       │
       ▼
┌──────────────────────┐
│ research-intent      │ ← 判断: 论文还是专利?
└──────────────────────┘
       │
       ▼ (专利流程)
       │
       ▼
┌──────────────────────────────────────────────┐
│ Step 1: 意图理解与可行性调研                   │
│  - research-idea-parser                       │
│  - research-feasibility-researcher           │
│  → 输出: 《XXX可行性评估报告.docx》           │
└──────────────────────────────────────────────┘
         │ (用户确认后)
         ▼
┌──────────────────────────────────────────────┐
│ Step 2: 深度调研与资料收集                    │
│  - research-deep-researcher (≥5篇真实文献)   │
│  → 输出: 《文献综述与资源列表.docx》         │
└──────────────────────────────────────────────┘
         │ (用户确认后)
         ▼
┌──────────────────────────────────────────────┐
│ Step 3: 核心观点与大纲起草                    │
│  - research-patent-drafting                   │
│  → 输出: 《XXX专利说明书.docx》              │
└──────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────┐
│ Step 4: 逻辑自洽与推演校验                   │
│  - research-consistency-checker              │
└──────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────┐
│ Step 5: 查重与重复率控制                     │
│  - research-plagiarism-detector              │
└──────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────┐
│ Step 6: 语言润色与去AI化                     │
│  - research-style-humanizer                  │
└──────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────┐
│ Step 7: 生成最终文档                         │
│  - 三份文档全部生成完毕                       │
│  - 清理过程中的代码文件 (.py, .js, .ts等)    │
└──────────────────────────────────────────────┘
         │
         ▼
    最终专利交付 (3份文档)
```

**⚠️ 专利流程关键规则 / Patent Process Key Rules**:
- 禁止: 任何一步未完成，不得进入下一步 / Prohibition: Cannot proceed to next step without completing current step
- 必须: 用户确认每一步结果后继续 / Requirement: Must wait for user confirmation after each step
- 必须: 用户要求重新编写时，忘记历史内容，重新开始 / Requirement: When user requests rewrite, forget history and start fresh

---

## 核心 Skills 详解 / Core Skills Details

### 1. arxiv - 学术论文预印本平台搜索 / ArXiv Search

**功能 / Function**: 搜索和检索 arXiv 学术论文。/ Search and retrieve academic papers from arXiv.

**核心能力 / Core Capabilities**:
- 使用 arXiv API 搜索论文 (无需 API Key) / Search papers via arXiv API (no API key needed)
- 解析 Atom XML 获取元数据 / Parse Atom XML for metadata
- 生成 BibTeX 引用 / Generate BibTeX citations
- 结合 Semantic Scholar 获取引用数和关联论文 / Combine with Semantic Scholar for citations and related papers

**搜索源优先级 / Search Source Priority**:
| 优先级 / Priority | 来源 / Source | 适用 /适用 |
|----------|------------|
| 1 | arXiv API | STEM 技术类论文 |
| 2 | Semantic Scholar API | 引用数据、关联论文 |
| 3 | Crossref API | 期刊文章 (无限速) |

**⚠️ 注意事项 / Notes**:
- 搜索间隔需 ≥3 秒 / Rate limit: ≥3 seconds between requests
- 多词查询建议使用 Crossref / Use Crossref for multi-word queries
- 详见 `arxiv/SKILL.md` 获取完整使用说明

### 2. docx - Word 文档处理 / Word Document Processing

**功能 / Function**: 创建、编辑、转换 Word 文档 (.docx)。/ Create, edit, convert Word documents.

**核心能力 / Core Capabilities**:
- 使用 docx-js 创建新文档 / Create new documents with docx-js
- 解包 → 编辑 XML → 打包编辑现有文档 / Edit existing documents (unpack → edit XML → repack)
- 格式处理 (页眉页脚、目录、表格、图像) / Format handling (headers/footers, TOC, tables, images)
- 追踪修订与批注 / Track changes and comments
- 中英双语文档支持 / Bilingual document support (Chinese/English)

**关键规则 / Critical Rules**:
- 必须显式设置页面尺寸 (docx-js 默认 A4) / Must set page size explicitly (docx-js defaults to A4)
- 必须使用 DXA 单位设置表格宽度 (百分比在 Google Docs 中失效) / Must use DXA for table width (percentages break in Google Docs)
- 禁止使用 unicode 符号作为列表标记 / Never use unicode symbols as bullet markers

**⚠️ 注意事项 / Notes**: 详见 `docx/SKILL.md` 获取完整使用说明和坑点指南。

### 3. paper-writing - 论文撰写 / Paper Writing

**功能 / Function**: 根据调研结果撰写完整的学术论文。/ Write complete academic papers based on research findings.

**支持格式 / Supported Formats**: Markdown, LaTeX, Word (.docx)

**特殊工作流 / Special Workflows**:
- **文献计量类论文**: bibliometric analysis、VOSviewer、Bibliometrix 等 → 需使用调整后的工作流 (详见 `references/bibliometric-paper-workflow.md`)
- **语言水平调整**: 降级/升级论文的学术水平 (详见 `references/undergraduate-level-adjustment.md`)

**搜索策略优先级 / Search Strategy Priority**:
1. Crossref API (首选 —— 无限速) / Crossref API (preferred — no rate limit)
2. Semantic Scholar API (备选，间隔 ≥1.5s) / Semantic Scholar API (backup, ≥1.5s delay)
3. arXiv API (仅 STEM) / arXiv API (STEM only)

**⚠️ 重要 / Important**:
- 搜索社交科学/人文时不要依赖 arXiv / Don't rely on arXiv for social sciences/humanities
- 检索式必须可验证 / Search queries must be verifiable
- 源数据必须导出为 CSV/Excel / Source data must be exported as CSV/Excel

### 4. research-intent - 意图判断 / Intent Detection

**功能 / Function**: 识别用户的写作意图是论文还是专利。/ Identifies whether user's writing intent is for a paper or patent.

**决策逻辑 / Decision Logic**:
- 用户需求是 **专利** → 调用 `patent-writing` SKILL / User needs **patent** → call `patent-writing` SKILL
- 用户需求是 **论文** → 调用 `paper-writing` SKILL / User needs **paper** → call `paper-writing` SKILL
- 用户未指明 → 自由发挥 / User unspecified → use discretion

### 5. research-idea-parser - 研究想法解析 / Research Idea Parser

**功能 / Function**: 将用户的原始研究想法解析为结构化的研究简报。/ Parses user's raw research idea into a structured research brief.

**输出格式 / Output Format**:
```json
{
  "research_question": "...",
  "domain": "Computer Science | Medical Sciences | Geography | ...",
  "sub_domain": "...",
  "key_concepts": ["...", "...", "..."],
  "potential_methods": ["...", "...", "..."],
  "research_type": "empirical | theoretical | applied",
  "confidence": "high | medium | low",
  "clarification_needed": ["...", "..."],
  "suggested_search_terms": ["...", "..."]
}
```

**使用场景 / Use Case**: 用户提供了初步研究想法，需要结构化理解时。/ When user provides initial research idea requiring structured understanding.

### 6. research-feasibility-researcher - 可行性调研 / Feasibility Researcher

**功能 / Function**: 评估研究想法是否可行，通过搜索相关工作来评估创新性。/ Evaluates research idea feasibility by searching related work to assess novelty.

**核心决策 / Core Decisions**:
- **proceed**: 想法可行，有足够创新空间 / Idea is feasible with sufficient room for innovation
- **pivot**: 需要调整方向，存在高度相似的现有工作 / Need to pivot direction due to highly similar existing work
- **abandon**: 想法已被充分研究，需要完全放弃 / Idea has been thoroughly researched and should be abandoned

**输出格式 / Output Format**:
```json
{
  "feasibility_score": "high | medium | low",
  "related_work_summary": {...},
  "novelty_assessment": {...},
  "recommendation": "proceed | pivot | abandon"
}
```

**输出文档 / Output Document**: `docx` 生成 **《XXX可行性评估报告.docx》** / Generates **《XXX Feasibility Assessment Report.docx》** via `docx`

### 7. research-deep-researcher - 深度文献调研 / Deep Literature Researcher

**功能 / Function**: 进行全面的文献调研，至少收集15篇真实文献。/ Conducts comprehensive literature research, collecting at least 15 real papers.

**核心原则 / Core Principles**:
1. **先广后深** - 广泛搜索后深入分析 / Broad first, then deep - extensive search followed by in-depth analysis
2. **验证引用** - 永不信任AI生成的引用 / Validate citations - never trust AI-generated citations
3. **提取洞见** - 不仅收集，还要分析 / Extract insights - not just collect, but analyze
4. **构建结构化知识** - 为下游使用组织信息 / Build structured knowledge - organize information for downstream use

**搜索策略 / Search Strategy**:
| 轮次 / Round | 目标 / Target |
|------|------|
| Round 1 | 广度搜索，找到相关论文 / Broad search to find related papers |
| Round 2 | 深度搜索，基于第一轮术语扩展 / Deep search, expanding on Round 1 terms |
| Round 3 | ���对性搜索，填补空白 / Targeted search to fill gaps |

**搜索源选择 / Search Source Selection**:
| 学科领域 / Domain | 推荐搜索源 / Recommended Sources |
|----------|------------|
| 医学 / Medicine | PubMed, Cochrane Library, Google Scholar |
| 自然科学 / Natural Sciences | Web of Science, Scopus, arXiv |
| 工程 / Engineering | IEEE Xplore, Scopus, Engineering Village |
| 计算机科学 / Computer Science | ACM Digital Library, arXiv, DBLP |
| 社会科学 / Social Sciences | JSTOR, SSRN, Sociological Abstracts |
| 地理/遥感 / Geography/Remote Sensing | Web of Science, GeoRef, ISPRS, IEEE Xplore |
| 石油地质 / Petroleum Geology | SPE, SEG, AAPG, OnePetro |

**输出 / Output**: `docx` 生成 **《文献综述与资源列表.docx》** / Generates **《Literature Review and Resource List.docx》** via `docx`

**⚠️ 重要 / Important**: 搜索社交科学、人文学科时，**不要依赖 arXiv** - 它主要覆盖CS/物理/数学，社交科学文献在期刊数据库中，需要使用 Crossref API。/ When searching social sciences and humanities, **do not rely on arXiv** - it mainly covers CS/physics/math, social science literature is in journal databases, use Crossref API.

### 8. research-paper-drafting - 论文撰写 / Paper Drafting

**功能 / Function**: 根据调研结果撰写完整的论文或专利草稿。/ Writes complete paper or patent draft based on research findings.

**支持格式 / Supported Formats**:
- Markdown (纯文本格式 / plain text format)
- LaTeX (学术会议格式，如 NeurIPS, ICML / academic conference format like NeurIPS, ICML)
- Word (.docx) - 需调用 `docx` skill / requires `docx` skill

**支持领域 / Supported Domains**:
| 领域 / Domain | 典型结构 / Typical Structure |
|------|----------|
| 计算机科学 / Computer Science | Abstract, Introduction, Related Work, Method, Experiments, Results, Conclusion |
| 社会科学 / Social Sciences | Abstract, Introduction, Literature Review, Theoretical Framework, Methodology, Findings, Discussion |
| 地理/遥感 / Geography/Remote Sensing | Abstract, Introduction, Study Area, Data and Methods, Results, Discussion |
| 地质学 / Geology | Abstract, Introduction, Geological Setting, Data and Methods, Results, Discussion |
| 石油科学 / Petroleum Science | Abstract, Introduction, Theory/Background, Methodology, Results/Analysis |

**核心原则 / Core Principles**:
1. **先有故事，再有结构** - 每篇论文需要一个叙事 / Story first, structure second - each paper needs a narrative
2. **基于证据** - 每个主张必须有数据支持 / Evidence-based - each claim must have data support
3. **遵循模板** - 匹配目标期刊/会议格式 / Follow template - match target journal/conference format
4. **迭代精炼** - 草稿、审查、修改 / Iterative refinement - draft, review, revise

### 9. research-consistency-checker - 逻辑一致性检查 / Consistency Checker

**功能 / Function**: 验证论文的逻辑一致性确保所有主张都有证据支持。/ Verifies logical consistency of paper, ensuring all claims have evidence support.

**检查维度 / Check Dimensions**:
- **Claim-Evidence 映射 / Mapping**: 每个主张是否有证据支持 / Each claim has evidence support
- **逻辑矛盾 / Logical Contradiction**: 各章节之间是否存在逻辑矛盾 / Logical contradictions between sections
- **术语一致性 / Terminology Consistency**: 同一概念是否用词统一 / Same concept uses consistent terminology
- **可追溯性 / Traceability**: 结果是否能追溯到实验 / Results can be traced to experiments

**输出格式 / Output Format**:
```json
{
  "validation_results": {
    "overall_status": "pass | fail | needs_revision",
    "issues": [
      {
        "type": "claim_evidence_mismatch | logical_contradiction | terminology_inconsistency",
        "severity": "critical | major | minor",
        "location": "section.paragraph",
        "description": "...",
        "suggested_fix": "..."
      }
    ],
    "statistics": {...}
  }
}
```

### 7. research-plagiarism-detector - 查重检测 / Plagiarism Detector

**功能 / Function**: 检测论文与现有文献的相似度。/ Detects similarity between paper and existing literature.

**阈值标准 / Threshold Standards**:
| 相似度 / Similarity | 严重程度 / Severity | 行动 / Action |
|--------|----------|------|
| > 50% | 严重 / Severe | 必须重写 / Must rewrite |
| 30-50% | 高 / High | 需要大幅改写 / Needs significant rewriting |
| 15-30% | 中 / Medium | 建议改写 / Recommend rewriting |
| < 15% | 低 / Low | 可接受 / Acceptable |

**分章节阈值 / Per-Section Thresholds**:
| 章节 / Section | 允许相似度 / Allowed Similarity |
|------|------------|
| Abstract | < 10% |
| Introduction | < 20% |
| Related Work | < 30% |
| Method | < 15% |
| Results | < 10% |
| Conclusion | < 15% |

### 8. research-style-humanizer - 语言润色 / Style Humanizer

**功能 / Function**: 移除AI写作痕迹，创造更自然的学术文风。/ Removes AI writing痕迹, creates more natural academic writing style.

**AI 模式检测 / AI Pattern Detection**:
1. 过渡词过度使用 (`furthermore`, `moreover`, `additionally`) / Overuse of transition words
2. 冗余套话 (`It is important to note that...`) / Redundant phrases
3. 句式重复 (`We first... We then... We finally...`) / Sentence pattern repetition
4. 过度修饰 (`significantly`, `remarkably`, `notably`) / Over-modification
5. 通用开头 (`In recent years...`, `It is well known...`) / Generic openings

**中文论文特定模式 / Chinese Paper Specific Patterns** (需删除 / Need to remove):
| 模式 / Pattern | 行动 / Action |
|------|------|
| `需要指出的是，` | 删除，直接开始 / Remove, start directly |
| `值得关注的是，` | 删除，直接开始 / Remove, start directly |
| `值得注意的是，` | 删除，直接开始 / Remove, start directly |
| `众所周知，` | 替换为具体引用或删除 / Replace with specific citation or remove |

**输出 / Output**: AI检测分数改善（目标 < 0.4），人类写作分数提升 / AI detection score improvement (target < 0.4), human writing score improvement

---

## 其他辅助 Skills / Other Auxiliary Skills

### blogwatcher
博客内容监控与检索工具。 / Blog content monitoring and retrieval tool.

### llm-wiki
LLM 相关知识库检索工具。 / LLM-related knowledge base retrieval tool.

---

## 模板文件 / Template Files

模板文件位于各技能目录下的 `references/` 目录：/ Template files are located in the `references/` directory of each skill:

- `paper-writing/references/论文模板.docx` - 论文模板 / Paper template
- `patent-writing/references/专利模板.docx` - 专利模板 / Patent template
- `docx/scripts/office/` - Office 文档处理脚本 / Office document processing scripts
- `docx/scripts/office/schemas/` - XML Schema 文件 / XML Schema files

---

## 快速开始 / Quick Start

1. **明确用户意图** - 使用 `research-intent` 判断是论文还是专利 / **Clarify user intent** - use `research-intent` to determine paper or patent
2. **结构化想法** - 使用 `research-idea-parser` 解析研究想法 / **Structure idea** - use `research-idea-parser` to parse research idea
3. **可行性评估** - 使用 `research-feasibility-researcher` 评估可行性 / **Feasibility assessment** - use `research-feasibility-researcher` to evaluate feasibility
4. **深度调研** - 使用 `research-deep-researcher` 进行文献调研 / **Deep research** - use `research-deep-researcher` for literature research
5. **生成文档** - 使用 `research-paper-drafting` 或 `patent-writing` 撰写 / **Generate document** - use `research-paper-drafting` or `patent-writing` to write
6. **质量检查** - 依次使用 `consistency-checker`、`plagiarism-detector`、`style-humanizer` / **Quality check** - use `consistency-checker`, `plagiarism-detector`, `style-humanizer` in sequence
7. **最终输出** - 使用 `docx` 生成最终的 Word 文档 / **Final output** - use `docx` to generate final Word document

---

## 技术依赖 / Technical Dependencies

- **npm docx**: 用于生成新的 Word 文档 / Used to generate new Word documents
- **Python**: 脚本处理 (pandoc, soffice, unpack/pack) / Script processing (pandoc, soffice, unpack/pack)
- **LibreOffice**: PDF 转换 / PDF conversion
- **Web Search APIs**: Crossref, Semantic Scholar, arXiv, Google Scholar

---

# Agent 角色定义 / Agent Role Definition

将 **skills\for_system_prompt.md** 的内容完整拷贝，复制到能够注入到 system prompt 的文件当中，比如 SOUL.md、CLAUDE.md、IDENTITY.md、AGENT.md 等 / Copy the following content entirely and paste it into files that can be injected into system prompts, such as SOUL.md, CLAUDE.md, IDENTITY.md, AGENT.md, etc.

---

# 更新日志 / Changelog

## 2025-05-05

**更新的 skills**:
- `arxiv/SKILL.md` - 新增学术论文预印本搜索工具
- `docx/SKILL.md` - 新增 Word 文档处理工具
- `paper-writing/SKILL.md` - 新增论文撰写工具

**主要变更**:
1. 重新组织核心 Skills 顺序，将最常用的 arxiv、docx、paper-writing 放在前面
2. 更新搜索策略优先级：Crossref API (首选) → Semantic Scholar API → arXiv API
3. 新增文献计量类论文特殊处理说明
4. 新增语言水平调整功能说明
5. 新增 API 检索坑点指南参考文档
6. 更新 Deep Research 要求：至少 15 篇真实文献