---
name: research-patent-drafting
title: Patent Drafting Module
description: "Draft Chinese invention patent documents following CNIPA standards."
version: 1.0.0
author: Sisyphus
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Research, Patent Drafting, IP, Chinese Patent, Domain-Agnostic, Geography, Remote Sensing, GIS, Geology, Petroleum]
    category: research
    related_skills: [research-idea-parser, research-feasibility-researcher, research-deep-researcher, research-consistency-checker, research-plagiarism-detector, docx]
    requires_toolsets: [llm, files]
---

# Patent Drafting Module

This module drafts complete Chinese invention patent documents following CNIPA (China National Intellectual Property Administration) standards. It uses a **Claims-First** drafting logic where claims define the protection scope and the description must support every claim element.

---

## When To Use This Skill

Use this skill when:
- **Need to write a patent** — Invention patent full-text including claims, description, abstract
- **User requests patent document** — Chinese patent format
- **Following patent workflow** — Working with research-patent-drafting workflow
- **Multiple sections** — Need cohesive writing across sections

---

## Core Philosophy

1. **Claims First** — Claims define protection scope, description supports all claims
2. **Full Support** — Every term in claims must appear and be defined in description
3. **Multiple Embodiments** — At least 2-3 embodiments with different configurations
4. **Parameter Ranges** — Every tunable parameter needs range + preferred value

---

## Input Format

```json
{
  "invention_title": "A method for ...",
  "research_question": "What technical problem does this solve?",
  "technical_field": "Remote sensing / Image processing / etc.",
  "prior_art_limitations": ["limitation1", "limitation2", "..."],
  "technical_solution": "How does the invention solve the problem?",
  "beneficial_effects": ["effect1", "effect2", "..."],
  "key_innovations": ["innovation1", "innovation2", "..."],
  "embodiments": [
    {"name": "Embodiment 1", "config": "...", "parameters": {...}},
    {"name": "Embodiment 2", "config": "...", "parameters": {...}}
  ],
  "literature_review": {...},
  "target_format": "Chinese Invention Patent"
}
```

---

## Output Format

```json
{
  "patent": {
    "title": "发明名称",
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

---

## Document Generation: Use `docx` Skill

To generate the final Word document (.docx), use the **`docx`** skill.

### How to Use docx Skill

1. **Load the docx skill** when you need to create a .docx file from the generated patent content
2. **The docx skill** handles:
   - Page setup (A4, Chinese patent margins)
   - Fonts (SimSun for body, SimHei for headings)
   - Heading styles (Heading 1, 2, 3)
   - Tables (with proper column widths including parameter tables)
   - Numbered lists for claims
   - Headers/footers and page numbers

### Integration Workflow

```
Patent Drafting (this skill) → Generate patent content in Markdown
                                        ↓
                               Load docx skill
                                        ↓
                               Create formatted .docx file
```

### Chinese Patent DOCX Specifics

When creating the Word document, use docx skill with:
- Page setup: A4 (21cm × 29.7cm)
- Margins: top/bottom 2.5cm, left/right 2.0cm (中国专利标准)
- Fonts: SimSun (宋体) for body, SimHei (黑体) for headings
- First page: special layout for 说明书摘要

### Example: Converting Patent to Word Document

```javascript
// Using docx skill's docx-js library
const { Document, Packer, Paragraph, TextRun, HeadingLevel } = require('docx');

const doc = new Document({
  sections: [{
    properties: { 
      page: { 
        size: { width: 11906, height: 16838 },  // A4
        margin: { top: 2520, right: 2880, bottom: 2520, left: 2880 }  // 2.5cm top/bottom, 2.0cm left/right (in DXA)
      } 
    },
    children: [
      // Title (发明名称)
      new Paragraph({ heading: HeadingLevel.HEADING_1, children: [new TextRun(patent.title)] }),
      // Abstract (说明书摘要)
      new Paragraph({ heading: HeadingLevel.HEADING_2, children: [new TextRun("说明书摘要")] }),
      new Paragraph(patent.abstract),
      // Claims (权利要求书)
      new Paragraph({ heading: HeadingLevel.HEADING_2, children: [new TextRun("权利要求书")] }),
      // ... claims content
    ]
  }]
});

await Packer.toBuffer(doc);
```

---

## Processing Steps

### Step 1: Load Patent Template

The patent template is defined in the external reference directory. **DO NOT hardcode template filename.**

**Template Directory:**
```
skills/research/research-flow/patent/references/
```

**How to Load Dynamically:**
1. List all `.docx` files in the references directory
2. If multiple templates exist, ask user which to use
3. If only one exists, use that one
4. Read the chosen template using docx skill
5. Parse to understand required sections and format
6. Adjust generated content to match template format

```python
def load_patent_template(user_preferred: str = None) -> PatentTemplate:
    """Load patent template dynamically from references directory."""
    
    ref_dir = "skills/research/research-flow/patent/references/"
    
    # List all .docx files in directory
    template_files = [f for f in os.listdir(ref_dir) if f.endswith('.docx')]
    
    if not template_files:
        raise ValueError("No template file found in references directory")
    
    # Use user-specified, or ask if multiple, or use first
    if user_preferred and user_preferred in template_files:
        template_path = os.path.join(ref_dir, user_preferred)
    elif len(template_files) == 1:
        template_path = os.path.join(ref_dir, template_files[0])
    else:
        # Ask user: "请选择模板: 1) xxx.docx 2) yyy.docx"
        template_path = ask_user_to_select(template_files)
    
    # Use docx skill to read the .docx file
    template = docx.read(template_path)
    
    return template
```

**Template Variables (from loaded template):**
- 发明名称 format
- 说明书摘要 structure
- 权利要求书 format (independent + dependent claims)
- 说明书 sections order
- 附图说明 format
- 具体实施方式 structure
- Parameter table format

**Note:** Templates are loaded dynamically - filename changes don't require skill updates.
            "format": "ama"
        },
        # Engineering (IEEE format)
        "IEEE": {
            "sections": ["Abstract", "Introduction", "Problem Formulation", "Proposed Method", "Experimental Results", "Discussion", "Conclusion", "References"],
            "page_limit": "varies",
            "format": "ieee"
        },
        # Social Sciences
        "SocialScience": {
            "sections": ["Abstract", "Introduction", "Literature Review", "Theoretical Framework", "Methodology", "Findings", "Discussion", "Conclusion", "References"],
            "page_limit": "varies",
            "format": "apa"
        },
        # Geography & Earth Sciences (commonly used formats)
        "Geography": {
            "sections": ["Abstract", "Introduction", "Study Area", "Data and Methods", "Results", "Discussion", "Conclusion", "References"],
            "page_limit": "varies",
            "format": "geography"
        },
        "EarthSciences": {
            "sections": ["Abstract", "Introduction", "Background", "Methodology", "Results", "Discussion", "Conclusions", "References"],
            "page_limit": "varies",
            "format": "elsevier"
        },
        # Remote Sensing & GIS
        "RemoteSensing": {
            "sections": ["Abstract", "Introduction", "Study Area", "Data", "Methodology", "Results", "Discussion", "Conclusion", "References"],
            "page_limit": "varies",
            "format": "rs-journal"
        },
        "GIScience": {
            "sections": ["Abstract", "Introduction", "Related Work", "Conceptual Framework", "Methodology", "Experiments", "Results", "Discussion", "Conclusion", "References"],
            "page_limit": "varies",
            "format": "giscience"
        },
        # Geology (commonly used formats)
        "Geology": {
            "sections": ["Abstract", "Introduction", "Geological Setting", "Data and Methods", "Results", "Discussion", "Conclusions", "References"],
            "page_limit": "varies",
            "format": "geology"
        },
        "Hydrogeology": {
            "sections": ["Abstract", "Introduction", "Study Area", "Hydrogeological Setting", "Methods", "Results", "Discussion", "Conclusions", "References"],
            "page_limit": "varies",
            "format": "hydrogeology"
        },
        "StructuralGeology": {
            "sections": ["Abstract", "Introduction", "Tectonic Background", "Data and Methods", "Structural Analysis", "Kinematic Evolution", "Implications", "Conclusions", "References"],
            "page_limit": "varies",
            "format": "structural"
        },
        # Oil & Gas Sciences (industry-standard formats)
        "SPE": {
            "sections": ["Abstract", "Introduction", "Theory/Background", "Methodology", "Results/Analysis", "Discussion", "Conclusions", "References"],
            "page_limit": "varies",
            "format": "spe"
        },
        "AAPG": {
            "sections": ["Abstract", "Introduction", "Geologic Setting", "Data and Methods", "Results", "Discussion", "Conclusions", "References"],
            "page_limit": "varies",
            "format": "aapg"
        },
        "PetroleumGeology": {
            "sections": ["Abstract", "Introduction", "Regional Geology", "Stratigraphy", "Structure", "Reservoir Characterization", "Hydrocarbon System", "Conclusions", "References"],
            "page_limit": "varies",
            "format": "petroleum"
        },
        # Earth Sciences
        "EarthSciences": {
            "sections": ["Abstract", "Introduction", "Background", "Methodology", "Results", "Discussion", "Conclusions", "References"],
            "page_limit": "varies",
            "format": "earth-sciences"
        },
        # General Scientific
        "General": {
            "sections": ["Abstract", "Introduction", "Background", "Methods", "Results", "Discussion", "Conclusion", "References"],
            "page_limit": "varies",
            "format": "generic"
        }
    }
    
    return structures.get(venue, structures["General"])
```

### Step 2: Write Abstract

The abstract must follow the 5-sentence formula:

```python
def write_abstract(contribution: Contribution, results: Results) -> str:
    """Write paper abstract following the 5-sentence formula."""
    
    prompt = f"""
    Write an abstract for a research paper following this formula:
    1. What you achieved: "We introduce...", "We demonstrate..."
    2. Why this is hard and important
    3. How you do it (with specialist keywords)
    4. What evidence you have
    5. Your most remarkable result
    
    Contribution: {contribution.one_sentence}
    Key Results: {results.summary}
    
    Write exactly 5 sentences. Be specific with numbers.
    """
    
    return llm.generate(prompt)
```

### Step 3: Write Introduction

Introduction structure:
- Problem statement (1-2 paragraphs)
- Current approaches and their limitations (1 paragraph)
- Our approach overview (1 paragraph)
- Contributions list (bullet points)
- Paper structure (1 paragraph)

```python
def write_introduction(problem: Problem, contribution: Contribution) -> str:
    """Write paper introduction."""
    
    prompt = f"""
    Write an introduction for a research paper.
    
    Problem: {problem.description}
    Key Limitation: {problem.limitations}
    
    Our Approach: {contribution.one_sentence}
    Contributions:
    {format_contributions(contribution.key_points)}
    
    Structure:
    1. Start with the broad problem area (1-2 sentences)
    2. Define the specific problem and why it matters (2-3 sentences)
    3. Discuss current approaches and their limitations (2-3 sentences)
    4. Present our approach and key idea (2-3 sentences)
    5. List contributions as bullet points
    6. Outline paper structure
    
    Write in formal academic style. Be specific.
    """
    
    return llm.generate(prompt)
```

### Step 4: Write Related Work

Organize by themes, not papers:

```python
def write_related_work(literature: LiteratureReview) -> str:
    """Write related work section organized by theme."""
    
    prompt = f"""
    Write a related work section organized by research theme.
    For each theme:
    1. Briefly describe the research direction
    2. Discuss key papers and their approaches
    3. Explain how your work differs
    
    Literature Review:
    {format_literature_by_theme(literature)}
    
    Your Research Question: {research_question}
    
    Organize as:
    ### Theme 1
    [Content]
    
    ### Theme 2
    [Content]
    
    Use proper citations. Don't list papers one by one.
    """
    
    return llm.generate(prompt)
```

### Step 5: Write Method

Must enable reimplementation:

```python
def write_method(method: MethodDescription, experiments: ExperimentConfig) -> str:
    """Write method section with full details."""
    
    prompt = f"""
    Write a method section that allows complete reimplementation.
    
    Method Overview: {method.overview}
    Key Components:
    {format_components(method.components)}
    
    Include:
    1. High-level overview (1-2 paragraphs)
    2. Formal description or algorithm
    3. All hyperparameters and settings
    4. Implementation details sufficient for reproduction
    
    Use mathematical notation where appropriate.
    Be precise about architecture and parameters.
    """
    
    return llm.generate(prompt)
```

### Step 6: Write Experiments

For each experiment, state:
- What claim it supports
- Setup and configuration
- Results

```python
def write_experiments(experiments: List[Experiment], results: Results) -> str:
    """Write experiments section."""
    
    prompt = f"""
    Write an experiments section.
    
    For each experiment:
    - What claim does it support?
    - What is the setup?
    - What are the results?
    
    Experiments:
    {format_experiments(experiments)}
    
    Results:
    {format_results(results)}
    
    Include:
    - Dataset descriptions
    - Evaluation metrics
    - Baselines compared
    - Implementation details
    
    Use tables for main results.
    """
    
    return llm.generate(prompt)
```

### Step 7: Write Results and Analysis

```python
def write_results(results: Results, analysis: Analysis) -> str:
    """Write results and analysis section."""
    
    prompt = f"""
    Write results and analysis sections.
    
    Main Results: {results.main}
    Additional Results: {results.additional}
    Analysis: {analysis.findings}
    
    Structure:
    1. Main results with tables/figures
    2. Statistical significance
    3. Key findings
    4. Additional analyses (ablation, etc.)
    5. Error analysis if applicable
    
    Report error bars and statistical tests.
    """
    
    return llm.generate(prompt)
```

### Step 8: Write Conclusion

```python
def write_conclusion(contribution: Contribution, results: Results, limitations: List[str]) -> str:
    """Write conclusion section."""
    
    prompt = f"""
    Write a conclusion section.
    
    Main Contribution: {contribution.one_sentence}
    Key Results: {results.summary}
    Limitations: {limitations}
    
    Include:
    1. Restate main contribution (different words from abstract)
    2. Summarize key findings (2-3 sentences, not a list)
    3. Discuss implications for the field
    4. Acknowledge limitations honestly
    5. Suggest future work (2-3 concrete next steps)
    
    Do NOT introduce new results or claims.
    """
    
    return llm.generate(prompt)
```

---

### Step 9: Patent Drafting (Alternative Output Format)

When the target is a patent rather than a research paper, use this structure instead of the paper sections above.

**⚠️ CRITICAL — Patent writing follows an INTERACTIVE 7-step workflow with user confirmation checkpoints.** Each step gates the next. You MUST produce the intermediate deliverable and WAIT for explicit user approval before advancing. If the user says "重新撰写" or corrects your approach, restart from Step 1. Skipping ahead to write the full patent in one pass will trigger user correction.

**Skill routing fallback.** The `patent-writing` and `research-intent` skills may fail to load via `skill_view` despite appearing in `skills_list`. If this happens, use `search_files` to locate them under `research/research-flow/` and `read_file` directly:
```
search_files(path="~/.hermes/profiles/research/skills/research", pattern="SKILL.md")
# Then read_file: research/research-flow/patent/SKILL.md
```

**Workflow (DO NOT SKIP):**

```
Step 1: Feasibility Study → Produce《XXX可行性评估报告.docx》→ WAIT for user confirmation
Step 2: Deep Research     → Produce《文献综述与资源列表.docx》→ WAIT for user confirmation
Step 3: Drafting           → Produce《XXX专利说明书.docx》(full patent)
Step 4: Logic Validation   → Cross-verify with research-consistency-checker
Step 5: Plagiarism Check   → Check with research-plagiarism-detector
Step 6: Polishing          → Humanize with research-style-humanizer
Step 7: Publishing         → Notify user of final file location
```

**Pitfall — Skill routing.** The `patent-writing` and `research-intent` skills may fail to load via `skill_view` despite appearing in `skills_list`. When this happens, locate them via `search_files` under `research/research-flow/` and read directly:
- Patent workflow: `research/research-flow/patent/SKILL.md`
- Intent router: `research/research-flow/SKILL.md`

**Pitfall — DO NOT skip ahead.** The most common mistake is writing a full patent in one pass without producing and getting confirmation on the intermediate Feasibility Report and Literature Review deliverables. Each step gates the next: user must explicitly approve Step 1 before Step 2 begins, and approve Step 2 before drafting begins. If the user says "重新撰写" or corrects your approach, restart from Step 1.

The patent follows a **Claims-First** drafting logic: claims define the protection scope, the description must support every claim element.

#### Patent Template Reference

The patent structure is defined in the external template directory:

**Template Directory:** `skills/research/research-flow/patent/references/`

Templates are loaded dynamically - any .docx file in this directory can be used as a template.

#### Claims Drafting Principles

1. **独立权利要求 (Independent Claim)**: 1 method claim covering all essential steps. Write at a broad-enough level that infringement requires all steps.
2. **从属权利要求 (Dependent Claims)**: Each one adds a single technical feature constraining the parent claim. One feature per claim.
3. **分层保护**: Claim 1 (broadest) → Claims 2-4 (index calculation details) → Claims 5-6 (morphological separation) → Claims 7-9 (skeletonization & width estimation).

```python
def draft_claims(method: MethodDescription) -> List[Claim]:
    """
    Draft claims following the umbrella-branch pattern:
    Claim 1: Covers all major steps in method sequence
    Claims 2-N: Each elaborates one step with mathematical detail
    
    Rules:
    - Claim 1 should mention all essential steps but NOT formulas
    - Dependent claims introduce specific formulas, parameters, thresholds
    - Last claims cover optional enhancements (sub-pixel, dry-channel detection)
    """
    pass
```

#### Description Writing Pattern

```
Background: 4-paragraph structure
  P1: Importance of the task and why it matters
  P2: Existing methods (classified by type), including their specific limitations for THIS problem
  P3: Summary of why existing methods fail for the target scenario
  P4: Introduce the technical gap — the missing capability

Technical Solution (发明内容):
  P1: State the technical problem to solve (3 bullet points max)
  P2: Overview of the method (numbered steps S1-S6)
  P3-N: Detailed description of each step with mathematical formulas

Embodiments (具体实施方式):
  - Embodiment 1: Primary sensor (e.g., Landsat 8 OLI) with full parameter listing
  - Embodiment 2: Alternative sensor (e.g., Sentinel-2 MSI) with parameter adjustments
  - Embodiment 3: Large-scale/deployment variant
  - Parameter Table: Summary of all configurable parameters with ranges and preferred values
```

#### Common Pitfalls in Patent Drafting

| Pitfall | Correction |
|---------|------------|
| Claim is too narrow (includes unnecessary details) | Move specifics to dependent claims; Claim 1 should be broad |
| Description doesn't support claims | Every claim term must appear and be defined in the description |
| Missing parameter ranges | Always provide a range AND a preferred value for every tunable parameter |
| Single embodiment only | Provide at least 2-3 embodiments with different configurations/sensors |
| No "technical problem" statement | Explicitly state what prior art fails to do and what technical effect you achieve |
| Claims written as descriptions | Each claim is ONE sentence starting with "根据权利要求X所述的方法，其特征在于，..." |
| Forgetting 有益效果 | List 4-5 specific, measurable improvements over prior art |

#### Deliverable Format: DOCX Output

When the user says "输出为Word"/"DOCX" or asks for a deliverable document, follow this sequence:

1. **First draft in Markdown** — Write the full patent (说明书 + 权利要求书 + 说明书摘要) as markdown text. Get the content right.
2. **Hand off to `docx` skill** — Load the `docx` skill and use it to generate the `.docx` files. Do NOT reach for python-docx directly; the `docx` skill uses `docx-js` (Node.js) and its SKILL.md has all the API patterns.
3. **Chinese patent DOCX specifics** — Consult `docx` skill reference `references/chinese-patent-format.md` for page setup, fonts, margins, claim numbering, and parameter table styling.

**⚠️ CRITICAL: The 7-step workflow in `research-flow/patent/SKILL.md` is MANDATORY.** Do NOT skip steps or write the full patent in a single pass. The user MUST confirm each intermediate deliverable (feasibility report → literature review → patent draft) before the next step proceeds. Skipping steps will trigger user correction. Each step produces a standalone `.docx` file via the `docx` skill.

#### Patent-Specific Quality Checklist

- [ ] Claim 1 covers all essential steps without unnecessary detail
- [ ] Each dependent claim adds exactly one additional feature
- [ ] Every parameter mentioned in claims has a range + preferred value in description
- [ ] At least 2 embodiments with different sensor configurations
- [ ] "要解决的技术问题" clearly identifies 2-3 specific prior-art failures
- [ ] "有益效果" lists 4-5 measurable improvements
- [ ] Parameter table included (range + preferred value + explanation)
- [ ] Claims use the correct format: one sentence, transition at "其特征在于"
- [ ] Description supports all elements in the claims (no orphan terms)

---

## Two-Pass Refinement Pattern

### Pass 1: Write + Immediate Refine

For each section, write complete draft then immediately refine:

```python
def write_section_with_refine(section: str, context: dict) -> str:
    """Write section then immediately refine in same context."""
    
    # Write first draft
    draft = write_section(section, context)
    
    # Refine immediately
    refined = refine_section(section, draft, context)
    
    return refined
```

### Pass 2: Global Refinement

After all sections written, do cross-section refinement:

```python
def global_refinement(paper: Paper) -> Paper:
    """Refine paper with full context."""
    
    for section in paper.sections:
        refined = refine_with_context(
            section,
            full_paper_context
        )
    
    return paper
```

---

## Template Integration

Load and follow the target template:

---

## Example Output Structure

```json
{
  "paper": {
    "title": "Dynamic Example Selection for Chain-of-Thought Prompting",
    "abstract": "We introduce DES (Dynamic Example Selection), a novel method... Extensive experiments on five benchmarks show that DES improves reasoning performance by 15% over random selection... Our method provides a simple yet effective way to improve few-shot reasoning...",
    "introduction": {
      "content": "Large language models have shown remarkable capabilities...",
      "contributions": [
        "We propose DES, a novel method for dynamic example selection in CoT prompting",
        "We conduct extensive experiments on five reasoning benchmarks",
        "We show that similarity-based selection significantly outperforms random selection"
      ]
    },
    "related_work": [...],
    "method": {...},
    "experiments": {...},
    "results": {...},
    "conclusion": {...}
  }
}
```

---

## Quality Checklist

Before outputting patent draft, verify:

- [ ] Claim 1 covers all essential steps without unnecessary detail
- [ ] Each dependent claim adds exactly one additional feature
- [ ] Every parameter in claims has range + preferred value in description
- [ ] At least 2 embodiments with different configurations
- [ ] "要解决的技术问题" identifies 2-3 specific prior-art failures
- [ ] "有益效果" lists 4-5 measurable improvements
- [ ] Parameter table included (range + preferred value + explanation)
- [ ] Claims use correct format: one sentence, "其特征在于" transition
- [ ] Description supports all elements in claims (no orphan terms)

---

## Integration with Downstream Modules

The patent draft is used by:

1. **research-consistency-checker** — Validates logical consistency and claim-evidence mapping
2. **research-plagiarism-detector** — Checks for excessive similarity to existing patents/papers
3. **research-style-humanizer** — Removes AI writing patterns
4. **docx skill** — Generates final .docx file

---

## Error Handling

| Error | Handling |
|-------|----------|
| Missing technical solution | Request from upstream feasibility module |
| No prior art analysis | Request from deep-research module |
| Single embodiment only | Add more embodiments, note in revision |
| Claims not supported | Add missing terms to description |
| Parameter ranges missing | Add parameter table with range + preferred |
- [ ] Tables/figures properly referenced
- [ ] LaTeX compiles without errors (if LaTeX format)

---

## Final Output: Document Generation

This skill generates paper content in JSON/Text format. To create a professional Word document (.docx), use the **`docx`** skill.

### Output Options

| Output Type | Format | How to Generate |
|-------------|--------|-----------------|
| **JSON** | Structured data | Default output from this skill |
| **Markdown** | Text with MD formatting | Convert JSON to markdown |
| **LaTeX** | `.tex` file | Use template with LaTeX syntax |
| **Word (.docx)** | `.docx` file | **Use `docx` skill** ← Load the docx skill |

### Creating Word Document with docx Skill

When user requests Word document output:

1. **This skill** generates paper content (JSON/Text)
2. **Load docx skill** to create formatted .docx
3. **docx skill** handles all formatting:
   - Page setup (A4/Letter, margins)
   - Heading styles (Heading 1, 2, 3)
   - Tables with proper formatting
   - Numbered/bulleted lists
   - Page numbers, headers/footers
   - Table of contents (optional)

### Example Workflow

```python
# Step 1: Generate paper content (this skill)
paper_content = generate_paper(research_data, target_venue)

# Step 2: Load docx skill and create document
# (Use docx skill with the generated content)
docx_skill.create_document(paper_content, format=target_venue)
```

---

## Integration with Downstream Modules

The paper draft is used by:

1. **Consistency Checker** — Validates logical flow and claim-evidence mapping
2. **Plagiarism Detector** — Checks for excessive similarity to existing papers
3. **Style Humanizer** — Removes AI writing patterns

---

## Error Handling

| Error | Handling |
|-------|----------|
| Missing required data | Request from upstream modules |
| Template not found | Use default template, warn user |
| Section too long | Truncate or split |
| Inconsistent claims | Flag for revision |