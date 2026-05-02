# 研究流程技能参考文档

本文档详细列出 research-flow 目录下所有技能的功能和用途。

---

## 目录

1. [工作流路由技能](#1-research-intent---研究意图路由)
2. [论文写作流程](#2-paper---论文写作流程)
3. [专利写作流程](#3-patent---专利写作流程)
4. [研究想法解析器](#4-research-idea-parser---研究想法解析器)
5. [可行性研究员](#5-research-feasibility-researcher---可行性研究员)
6. [深度研究员](#6-research-deep-researcher---深度研究员)
7. [论文起草器](#7-research-paper-drafting---论文起草器)
8. [专利起草器](#8-research-patent-drafting---专利起草器)
9. [逻辑一致性检查器](#9-research-consistency-checker---逻辑一致性检查器)
10. [查重检测器](#10-research-plagiarism-detector---查重检测器)
11. [风格美化器](#11-research-style-humanizer---风格美化器)

---

## 1. research-intent - 研究意图路由

### 技能概述
- **名称**: research-intent
- **功能**: 根据用户的研究意图(写论文还是写专利)进行工作流程区分
- **输入**: 用户的原始请求
- **输出**: 指向对应工作流的路由决策

### 核心功能

| 功能 | 描述 |
|------|------|
| 论文意图识别 | 检测用户请求是否为写论文 |
| 专利意图识别 | 检测用户请求是否为写专利 |
| 自由发挥模式 | 无明确意图时，不参考特定格式 |

### 工作流选择规则

```
用户需求 → 识别意图 → 选择工作流
    ↓
写专利 → patent/SKILL.md (7步流程)
    ↓
写论文 → paper/SKILL.md (7步流程)
    ↓
未指定 → 自由发挥
```

---

## 2. paper - 论文写作流程

### 技能概述
- **名称**: paper-writing
- **功能**: 完整的论文写作工作流，包含7个步骤
- **依赖技能**: research-idea-parser, research-feasibility-researcher, research-deep-researcher, research-paper-drafting, research-consistency-checker, research-plagiarism-detector, research-style-humanizer, docx
- **输出**: 最终论文文档 (.docx)

### 7步工作流

| 步骤 | 名称 | 技能调用 | 产出 |
|------|------|----------|------|
| Step 1 | 意图理解与可行性调研 | research-idea-parser + research-feasibility-researcher + docx | 《XXX可行性评估报告.docx》 |
| Step 2 | 深度调研与资料收集 | research-deep-researcher + docx | 《文献综述与资源列表.docx》 (≥15篇文献) |
| Step 3 | 核心观点与大纲起草 | research-paper-drafting | 《XXX.docx》 |
| Step 4 | 逻辑自洽与推演校验 | research-consistency-checker | 验证报告 |
| Step 5 | 查重与重复率控制 | research-plagiarism-detector | 相似度报告 |
| Step 6 | 语言润色与去AI化 | research-style-humanizer | 润色后文档 |
| Step 7 | 生成最终的文档 | - | 最终文档 + 清理代码文件 |

### 关键约束

- **Step 2 必须≥15篇文献**，否则直接退出并告知用户
- **每步需用户确认**后再继续下一步
- 模板从目录 `paper/references/` 动态加载

---

## 3. patent - 专利写作流程

### 技能概述
- **名称**: patent-writing
- **功能**: 完整的专利写作工作流，包含7个步骤（遵循中国发明专利标准）
- **依赖技能**: research-idea-parser, research-feasibility-researcher, research-deep-researcher, research-patent-drafting, research-consistency-checker, research-plagiarism-detector, research-style-humanizer, docx
- **输出**: 最终专利文档 (.docx)

### 7步工作流

| 步骤 | 名称 | 技能调用 | 产出 |
|------|------|----------|------|
| Step 1 | 意图理解与可行性调研 | research-idea-parser + research-feasibility-researcher + docx | 《XXX可行性评估报告.docx》 |
| Step 2 | 深度调研与资料收集 | research-deep-researcher + docx | 《文献综述与资源列表.docx》 (≥5篇文献) |
| Step 3 | 核心观点与大纲起草 | research-patent-drafting | 《XXX专利说明书.docx》 |
| Step 4 | 逻辑自洽与推演校验 | research-consistency-checker | 验证报告 |
| Step 5 | 查重与重复率控制 | research-plagiarism-detector | 相似度报告 |
| Step 6 | 语言润色与去AI化 | research-style-humanizer | 润色后文档 |
| Step 7 | 生成最终的文档 | - | 最终文档 + 清理代码文件 |

### 关键约束

- **Step 2 只需≥5篇文献**（与论文不同）
- 必须先进行UTF-8格式转换，防止乱码
- 模板从目录 `patent/references/` 动态加载
- 遵循中国国家知识产权局(CNIPA)标准
- 使用**权利要求优先**撰写逻辑

---

## 4. research-idea-parser - 研究想法解析器

### 技能概述
- **名称**: research-idea-parser
- **功能**: 将用户的研究想法解析为结构化的研究简报(brief)
- **领域**: 通用（ domain-agnostic），适用于所有学术领域
- **输出**: 结构化JSON研究简报

### 核心功能

| 功能 | 描述 |
|------|------|
| 提取核心研究问题 | 从用户输入中提取单一、清晰的研究问题 |
| 领域分类 | 识别主要学术领域和子领域 |
| 确定研究类型 | 区分经验性/理论性/应用性/混合研究 |
| 提取关键概念 | 识别3-7个核心概念 |
| 识别潜在方法 | 基于想法建议可能的研究方法 |
| 评估置信度 | 高/中/低 三级评估 |
| 生成澄清问题 | 列出需要澄清的问题 |

### 输出格式

```json
{
  "raw_idea": "原始用户输入",
  "research_question": "单一清晰的研究问题",
  "domain": " Medicine | Biology | Geography | Engineering | ...",
  "sub_domain": "具体子领域（如有）",
  "key_concepts": ["概念1", "概念2", "..."],
  "potential_methods": ["方法1", "方法2", "..."],
  "research_type": "empirical | theoretical | applied | mixed",
  "intended_application": "研究的应用场景",
  "confidence": "high | medium | low",
  "clarification_needed": ["问题1", "问题2", "..."],
  "suggested_search_terms": ["搜索词1", "搜索词2", "..."]
}
```

### 处理步骤

1. **Step 1**: 提取核心研究问题
2. **Step 2**: 分类领域（自然 Sciences/医学/工程/计算机/社会科学/地理&地球科学/遥感&GIS/地质/油气科学等）
3. **Step 3**: 确定研究类型
4. **Step 4**: 提取关键概念
5. **Step 5**: 识别潜在方法
6. **Step 6**: 评估置信度
7. **Step 7**: 生成澄清问题

### 下游集成

输出被以下技能使用：
- **Feasibility Researcher**: 使用 research_question, key_concepts, suggested_search_terms, domain
- **Deep Researcher**: 使用 domain, sub_domain, key_concepts, research_type
- **Paper Drafting**: 使用 research_question, intended_application, research_type

---

## 5. research-feasibility-researcher - 可行性研究员

### 技能概述
- **名称**: research-feasibility-researcher
- **功能**: 评估研究想法的可行性，搜索相关工作，评估创新性
- **输入**: research-idea-parser 的结构化简报
- **输出**: 可行性评估报告

### 核心功能

| 功能 | 描述 |
|------|------|
| 多源搜索 | 并行搜索学术数据库、GitHub、网页等 |
| 结果分类 | 区分完全相同/高度相似/相关/不同 |
| 创新性评估 | 识别创新点，评估与现有工作的差异 |
| 碰撞风险评估 | 评估与正在进行的工作冲突的可能性 |
| 可行性评分 | 高/中/低 三级评分 |
| 建议生成 | proceed/pivot/abandon 建议 |

### 输出格式

```json
{
  "research_question": "原始问题",
  "feasibility_score": "high | medium | low",
  "related_work_summary": {
    "papers": [{"title": "...", "year": 2024, "relevance": "high|medium|low", "key_insight": "..."}],
    "github_repos": [{"name": "...", "stars": 123, "relevance": "high|medium", "description": "..."}]
  },
  "novelty_assessment": {
    "innovation_points": ["创新点1", "创新点2"],
    "differentiation_from_existing": "与现有工作的差异",
    "risk_of_collision": "low | medium | high"
  },
  "recommendation": "proceed | pivot | abandon",
  "suggested_research_direction": "改进方向（如需）"
}
```

### 搜索策略（按领域）

| 领域 | 数据源 |
|------|--------|
| 医学 | PubMed, ClinicalTrials.gov, Google Scholar |
| 自然科学 | Web of Science, arXiv, Google Scholar |
| 工程 | IEEE Xplore, Google Scholar, Scopus |
| 社会科学 | JSTOR, Google Scholar, SSRN |
| 地理&地球科学 | Web of Science, GeoRef, JSTOR |
| 遥感&GIS | IEEE Xplore, ISPRS Journal, Remote Sensing |
| 地质 | GeoRef, Web of Science, GSL Publications |
| 油气科学 | SPE, SEG, AAPG, OnePetro |

### 处理步骤

1. **Step 1**: 多源并行搜索
2. **Step 2**: 分类搜索结果
3. **Step 3**: 创新性评估
4. **Step 4**: 可行性评分
5. **Step 5**: 生成建议

### 早退条件

- 发现完全相同的工作 → 立即建议 pivot/abandon
- 发现5+高度相关的论文 → 建议 pivot（需区分）
- 明确创新路径 → 自信 proceed

---

## 6. research-deep-researcher - 深度研究员

### 技能概述
- **名称**: research-deep-researcher
- **功能**: 进行深入的文献调研，收集论文，分析内容，组织发现
- **输入**: research-idea-parser 的简报 + feasibility-report
- **输出**: 完整的文献综述

### 核心功能

| 功能 | 描述 |
|------|------|
| 初始广度搜索 | 多角度并行搜索 |
| 引文图扩展 | 通过引用关系发现更多论文 |
| 深度论文分析 | 提取关键贡献、方法、结果、局限性 |
| 按主题组织 | 将论文分组到研究主题 |
| 识别研究缺口 | 发现未解决的问题和方法 |
| 生成BibTeX | 创建正确的引用条目 |

### 输出格式

```json
{
  "research_question": "原始问题",
  "literature_review": {
    "sections": [
      {
        "theme": "主题名称",
        "papers": [{"title": "...", "year": 2024, "authors": ["..."], "key_contribution": "...", "method": "...", "limitations": "..."}],
        "synthesis": "这些论文与您工作的关系"
      }
    ]
  },
  "bibliography": {
    "papers": [...],
    "bibtex_string": "..."
  },
  "research_gaps": [
    {"gap": "描述", "severity": "high|medium|low", "opportunity": "如何解决"}
  ],
  "methodology_options": [
    {"method": "...", "pros": "...", "cons": "...", "suitability": "high|medium|low"}
  ]
}
```

### 搜索策略（3轮）

**Round 1: 广度（并行）**
- 直接搜索、问题搜索、方法搜索、基线搜索

**Round 2: 深度（迭代）**
- 从找到的论文中提取新术语
- 识别关键作者
- 搜索引用最新相关工作的论文

**Round 3: 针对性（特定缺口）**
- 搜索未探索的特定组合
- 寻找相反发现的论文

### 处理步骤

1. **Step 1**: 初始广度搜索
2. **Step 2**: 引文图扩展
3. **Step 3**: 深度论文分析
4. **Step 4**: 按主题组织
5. **Step 5**: 识别研究缺口
6. **Step 6**: 生成BibTeX

### 质量检查

- [ ] 至少搜索3轮不同查询
- [ ] 审阅至少15-20篇论文
- [ ] 所有引用可验证（非幻觉）
- [ ] 按主题组织，非仅列出
- [ ] 识别明确的研究缺口
- [ ] 生成有效的BibTeX条目

---

## 7. research-paper-drafting - 论文起草器

### 技能概述
- **名称**: research-paper-drafting
- **功能**: 根据研究发现撰写完整的研究论文草稿
- **输入**: research_question, literature_review, experiment_results, target_venue
- **输出**: 结构化论文内容（JSON格式）

### 核心功能

| 功能 | 描述 |
|------|------|
| 动态模板加载 | 从 paper/references/ 目录加载模板文件 |
| 结构化输出 | 按目标期刊/会议格式输出 |
| 完整章节撰写 | 摘要/引言/方法/实验/结果/讨论/结论 |
| 表格和图表 | 生成实验结果表格 |
| 参考文献 | 生成参考文献列表 |
| docx集成 | 可调用docx技能生成Word文档 |

### 输出格式

```json
{
  "paper": {
    "title": "论文标题",
    "sections": {
      "abstract": "...",
      "introduction": "...",
      "related_work": "...",
      "method": "...",
      "experiments": "...",
      "results": "...",
      "analysis": "...",
      "conclusion": "...",
      "limitations": "..."
    },
    "tables": [...],
    "figures": [...],
    "references": "..."
  },
  "metadata": {
    "word_count": 4500,
    "sections_complete": ["abstract", "introduction", "method"],
    "sections_remaining": ["experiments", "results"]
  }
}
```

### 支持的领域格式

| 格式 | 适用领域 |
|------|----------|
| Nature/Science/Cell | 顶级综合期刊 |
| IEEE | 工程 |
| AMA | 医学 |
| APA | 社会科学 |
| Geography | 地理 |
| RemoteSensing | 遥感 |
| GIScience | 地理信息科学 |
| Geology | 地质 |
| Hydrogeology | 水文地质 |
| SPE | 石油工程 |
| AAPG | 美国石油地质学家 |
| PetroleumGeology | 石油地质 |

### 摘要5句公式

1. 完成了什么（"We introduce..."）
2. 为什么困难且重要
3. 如何做（带专业术语）
4. 有什么证据
5. 最显著的结果

### 处理步骤

1. **Step 1**: 动态加载论文模板（从 paper/references/）
2. **Step 2**: 撰写摘要（5句公式）
3. **Step 3**: 撰写引言（问题陈述→当前方法→我们的方法→贡献→结构）
4. **Step 4**: 撰写相关工作（按主题组织）
5. **Step 5**: 撰写方法（详细技术描述）
6. **Step 6**: 撰写实验（设置、基线、指标）
7. **Step 7**: 撰写结果与分析（表格、图表、解读）
8. **Step 8**: 撰写结论（总结、局限、 Future Work）

### docx技能集成

生成内容后，使用docx技能创建Word文档：
- A4页面设置
- 标题样式（Heading 1, 2, 3）
- 表格（带列宽）
- 编号和项目符号列表
- 图片和图表
- 页眉/页脚和页码
- 目录

---

## 8. research-patent-drafting - 专利起草器

### 技能概述
- **名称**: research-patent-drafting
- **功能**: 撰写中国发明专利文档，遵循CNIPA标准
- **输入**: invention_title, technical_field, prior_art, technical_solution, beneficial_effects, embodiments
- **输出**: 结构化专利内容（JSON格式）

### 核心功能

| 功能 | 描述 |
|------|------|
| 权利要求优先 | 先写权利要求，说明书必须支持所有权利要求 |
| 动态模板加载 | 从 patent/references/ 目录加载模板文件 |
| 完整专利结构 | 摘要/权利要求书/说明书/附图说明/具体实施方式 |
| 独立权利要求 | 撰写覆盖所有必要步骤的独立权利要求 |
| 从属权利要求 | 每个从属权利要求增加一个技术特征 |
| 多个实施例 | 至少2-3个不同配置的实施例 |
| 参数范围 | 每个可调参数需要范围+优选值 |
| docx集成 | 可调用docx技能生成Word文档 |

### 输出格式

```json
{
  "patent": {
    "title": "发明名称",
    "sections": {
      "abstract": "说明书摘要",
      "claims": "权利要求书",
      "description": {
        "technical_field": "技术领域",
        "background_art": "背景技术",
        "summary": "发明内容",
        "drawings": "附图说明",
        "embodiments": "具体实施方式"
      }
    },
    "tables": [...],
    "figures": [...],
    "references": "..."
  }
}
```

### 中国发明专利结构

| 部分 | 内容 |
|------|------|
| 说明书摘要 | 发明核心内容的简要说明 |
| 权利要求书 | 保护范围定义（独立+从属） |
| 说明书 | 技术领域、背景、发明内容、具体实施方式 |
| 附图说明 | 附图的功能和内容说明 |
| 具体实施方式 | 至少2-3个实施例，带参数 |

### 权利要求撰写原则

1. **独立权利要求**: 1个方法权利要求覆盖所有必要步骤，写得足够宽使得侵权需要所有步骤
2. **从属权利要求**: 每个增加一个技术特征约束父权利要求，一个特征一个权利要求

### 处理步骤

1. **Step 1**: 动态加载专利模板（从 patent/references/）
2. **Step 2**: 撰写权利要求书（先独立后从属）
3. **Step 3**: 撰写说明书摘要
4. **Step 4**: 撰写技术领域和背景技术
5. **Step 5**: 撰写发明内容（要解决的技术问题、技术方案、有益效果）
6. **Step 6**: 撰写附图说明
7. **Step 7**: 撰写具体实施方式（至少2-3个，带参数表）

### docx技能集成（专利专用）

使用docx技能创建Word文档时：
- 页面：A4 (21cm × 29.7cm)
- 页边距：上下2.5cm，左右2.0cm（中国专利标准）
- 字体：宋体正文，黑体标题
- 特殊首页布局（说明书摘要）

---

## 9. research-consistency-checker - 逻辑一致性检查器

### 技能概述
- **名称**: research-consistency-checker
- **功能**: 验证研究论文的逻辑一致性、声明-证据对齐、无矛盾
- **输入**: paper内容 + experiment_log
- **输出**: 验证报告

### 核心功能

| 功能 | 描述 |
|------|------|
| 提取所有声明 | 识别论文中所有显式和隐式声明 |
| 映射声明到证据 | 为每个声明找到支持证据 |
| 检查逻辑矛盾 | 使用多视角检查逻辑一致性 |
| 验证术语一致性 | 检查术语使用是否一致 |
| 验证可追溯性 | 确保每个结果可追溯到实验 |

### 输出格式

```json
{
  "validation_results": {
    "overall_status": "pass | fail | needs_revision",
    "issues": [
      {
        "type": "claim_evidence_mismatch | logical_contradiction | traceabiltiy_gap | inconsistent_terminology",
        "severity": "critical | major | minor",
        "location": "section.paragraph",
        "description": "...",
        "suggested_fix": "..."
      }
    ],
    "claim_evidence_map": {
      "claim_1": {"supported": true, "evidence": "experiment_1", "location": "results.paragraph_3"}
    },
    "statistics": {
      "total_claims": 15,
      "supported_claims": 14,
      "unsupported_claims": 1,
      "contradictions": 0,
      "terminology_inconsistencies": 2
    }
  },
  "revision_needed": true,
  "revision_instructions": "..."
}
```

### 问题分类

**Critical Issues（严重问题）**
| 类型 | 示例 | 行动 |
|------|------|------|
| 不支持的主要声明 | "Our method achieves SOTA" 无结果 | 要求证据或删除 |
| 矛盾 | 方法说X，结果显示非X | 解决矛盾 |
| 结果捏造 | 数字与实验日志不匹配 | 验证或纠正 |

**Major Issues（主要问题）**
| 类型 | 示例 | 行动 |
|------|------|------|
| 缺失证据 | 声明证据较弱 | 添加更多支持数据 |
| 术语不一致 | 同一概念不同称呼 | 标准化术语 |
| 可追溯性缺口 | 结果未链接到实验 | 添加实验引用 |

**Minor Issues（次要问题）**
| 类型 | 示例 | 行动 |
|------|------|------|
| 模糊语言 | "显著改善" 无数字 | 添加具体数字 |
| 格式不一致 | 表格格式不同 | 标准化 |

### 多代理验证模式

```
输入: 论文草稿
  ↓
提取所有声明 (Agent 1)
  ↓
映射到证据 (Agent 2)
  ↓
检测矛盾 (Agent 3)
  ↓
验证术语 (Agent 4)
  ↓
综合 → 输出验证报告
```

### 处理步骤

1. **Step 1**: 提取所有声明
2. **Step 2**: 映射声明到证据
3. **Step 3**: 检查逻辑矛盾
4. **Step 4**: 验证术语一致性
5. **Step 5**: 验证可追溯性

---

## 10. research-plagiarism-detector - 查重检测器

### 技能概述
- **名称**: research-plagiarism-detector
- **功能**: 检测论文与现有文献的相似度，确保论文原创性
- **输入**: paper内容 + reference_papers + threshold
- **输出**: 相似度报告

### 核心功能

| 功能 | 描述 |
|------|------|
| 收集参考论文 | 从文献综述中收集相关论文 |
| 计算文本相似度 | 基于embedding的余弦相似度 |
| 计算语义相似度 | 想法层面的相似度检测 |
| 识别问题区域 | 找出特定高相似度段落 |
| 阈值行动 | 基于阈值判断是否需要修改 |
| 提供改写建议 | 为问题区域提供改写建议 |

### 输出格式

```json
{
  "overall_similarity": 0.08,
  "status": "pass | needs_revision | critical",
  "sections": [
    {
      "section": "introduction",
      "similarity_score": 0.12,
      "issues": [
        {
          "type": "textual_similarity | semantic_similarity | idea_similarity",
          "severity": "high | medium | low",
          "location": "paragraph_3",
          "matching_paper": "Paper Title",
          "matched_text": "...",
          "suggested_rewrite": "..."
        }
      ]
    }
  ],
  "statistics": {
    "total_sections_checked": 6,
    "sections_with_issues": 2,
    "high_similarity_regions": 0,
    "medium_similarity_regions": 3
  },
  "recommendations": [
    "Rewrite introduction paragraph 3-5 to reduce similarity",
    "Add more original analysis in results section"
  ]
}
```

### 相似度阈值

| 阈值 | 描述 |
|------|------|
| 高相似度 | >30% - 需要立即修改 |
| 中相似度 | 15-30% - 建议修改 |
| 低相似度 | <15% - 可接受 |

### 处理步骤

1. **Step 1**: 收集参考论文
2. **Step 2**: 计算文本相似度
3. **Step 3**: 计算语义相似度
4. **Step 4**: 识别问题区域

---

## 11. research-style-humanizer - 风格美化器

### 技能概述
- **名称**: research-style-humanizer
- **功能**: 移除AI写作模式，创建自然、类人的学术文章
- **输入**: paper内容 + target_style + aggression_level
- **输出**: 风格美化后的论文

### 核心功能

| 功能 | 描述 |
|------|------|
| AI模式检测 | 识别常见AI写作模式 |
| 移除过渡词 | 减少过度使用的过渡词 |
| 移除 hedge 短语 | 移除"it is important to note"等 |
| 改变句式结构 | 增加句式多样性 |
| 移除过度限定词 | 减少"significantly"等过度使用 |
| 替换通用开场 | 替换"in recent years"等 |
| 保持准确性 | 确保技术内容不变 |
| AI检测评分 | 评估修改前后AI概率 |

### 输出格式

```json
{
  "status": "success | needs_iteration",
  "humanized_sections": {
    "abstract": {
      "original_word_count": 150,
      "humanized_word_count": 145,
      "changes_made": [
        "Removed excessive transition words",
        "Added more direct phrasing",
        "reduced pattern repetition"
      ]
    }
  },
  "statistics": {
    "total_sections": 6,
    "sections_modified": 6,
    "word_count_change": "-5%",
    "pattern_reduction": {
      "transition_words": "-40%",
      "sentence_length_variance": "+15%",
      "pattern_markers": "-60%"
    }
  },
  "ai_detection_score": {
    "before": 0.85,
    "after": 0.35,
    "improvement": "-50%"
  },
  "human_score": {
    "before": 0.2,
    "after": 0.75,
    "improvement": "+55%"
  }
}
```

### 检测的AI模式

| 模式 | 示例 | 严重程度 |
|------|------|----------|
| 过渡词过度 | "furthermore", "moreover", "additionally" | 高（>4个） |
| hedge短语 | "it is important to note", "it should be noted" | 中 |
| 句式重复 | 每句都以相同结构开头 | 中 |
| 过度限定 | "significantly", "remarkably", "notably" | 中 |
| 通用开场 | "in recent years", "it is well known" | 低 |

### 处理步骤

1. **Step 1**: 识别AI模式
2. **Step 2**: 人类化文本（基于检测到的模式）

### aggression_level

| 级别 | 描述 |
|------|------|
| light | 轻度修改，保留更多原文 |
| medium | 中等修改，平衡自然度和准确性 |
| aggressive | 激进修改，最大化人类化 |

---

## 技能依赖关系图

```
用户请求
    ↓
research-intent (意图路由)
    ↓
    ├─→ paper (论文工作流)
    │       ↓
    │       research-idea-parser
    │       ↓
    │       research-feasibility-researcher
    │       ↓ (可行)
    │       research-deep-researcher (≥15篇)
    │       ↓
    │       research-paper-drafting
    │       ↓
    │       research-consistency-checker
    │       ↓
    │       research-plagiarism-detector
    │       ↓
    │       research-style-humanizer
    │       ↓
    │       docx → 最终论文
    │
    └─→ patent (专利工作流)
            ↓
            research-idea-parser
            ↓
            research-feasibility-researcher
            ↓ (可行)
            research-deep-researcher (≥5篇)
            ↓
            research-patent-drafting
            ↓
            research-consistency-checker
            ↓
            research-plagiarism-detector
            ↓
            research-style-humanizer
            ↓
            docx → 最终专利
```

---

## 文件结构

```
research-flow/
├── SKILL.md                    # research-intent 路由技能
├── paper/
│   ├── SKILL.md                # 论文写作工作流
│   └── references/             # 论文模板目录
│       └── 论文模板.docx
├── patent/
│   ├── SKILL.md                # 专利写作工作流
│   └── references/             # 专利模板目录
│       └── 专利模板.docx
├── research-idea-parser/
│   └── SKILL.md
├── research-feasibility-researcher/
│   └── SKILL.md
├── research-deep-researcher/
│   └── SKILL.md
├── research-paper-drafting/
│   └── SKILL.md
├── research-patent-drafting/
│   └── SKILL.md
├── research-consistency-checker/
│   └── SKILL.md
├── research-plagiarism-detector/
│   └── SKILL.md
└── research-style-humanizer/
    └── SKILL.md
```

---

## 关键约束总结

| 技能 | 关键约束 |
|------|----------|
| paper | Step2需≥15篇文献，每步需用户确认 |
| patent | Step2需≥5篇文献，UTF-8转换，权利要求优先 |
| research-idea-parser | 保持领域无关性 |
| research-feasibility | 早退条件：完全相同或5+高度相关 |
| research-deep-researcher | 至少3轮搜索，15-20篇论文 |
| research-paper-drafting | 动态加载模板，5句摘要公式 |
| research-patent-drafting | Claims-First，2-3个实施例，参数范围 |
| research-consistency | 多代理验证，4种检查类型 |
| research-plagiarism | 阈值判断：30%/15% |
| research-style-humanizer | 保持技术准确性 |

---

*本文档由 Sisyphus 自动生成*