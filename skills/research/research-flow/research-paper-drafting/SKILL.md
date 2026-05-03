---
name: research-paper-drafting
title: Paper Drafting Module
description: "Write research paper drafts for any academic domain following specified templates."
version: 1.1.0
author: Sisyphus
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Research, Paper Writing, Academic Writing, Domain-Agnostic, Geography, Remote Sensing, GIS, Geology, Petroleum, Hydrogeology]
    category: research
    related_skills: [research-deep-researcher, research-consistency-checker, research-plagiarism-detector, research-style-humanizer]
    requires_toolsets: [llm, files]
---

# Paper Drafting Module

This module writes complete research paper drafts based on research findings, following specified templates and incorporating all relevant research.

---

## When To Use This Skill

Use this skill when:
- **Need to write a complete paper** — From outline to full draft
- **Need to write a patent** — Invention patent full-text including claims, description, abstract, and multiple embodiments
- **Have research results** — Need to convert to paper or patent form
- **Following a template** — Need to match specific format
- **Multiple sections** — Need cohesive writing across sections

---

## Core Philosophy

1. **Story first, then structure** — Every paper needs a narrative
2. **Evidence-based** — Every claim must be supported by data
3. **Template-following** — Match the target venue's format
4. **Iterative refinement** — Draft, review, revise

---

## Input Format

```json
{
  "research_question": "Can dynamic example selection in chain-of-thought prompting improve reasoning performance?",
  "literature_review": {...},
  "experiment_results": {...},
  "target_venue": "Nature | Science | Cell | IEEE journals | ACM journals | Medical journals | General scientific | custom",
  "template": {
    "format": "latex | markdown",
    "style": "neurips2025 | icml2025 | custom",
    "page_limit": 8
  },
  "contribution": {
    "one_sentence": "We introduce dynamic example selection for chain-of-thought prompting that improves reasoning performance by selecting similar examples based on embedding similarity.",
    "key_points": [
      "Novel similarity-based example selection method",
      "Evaluation on 5 reasoning benchmarks",
      "Shows 15% improvement over random selection"
    ]
  }
}
```

---

## Output Format

```json
{
  "paper": {
    "title": "Dynamic Example Selection for Chain-of-Thought Prompting",
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

After generating the paper content (JSON format above), you can convert it to a professional Word document using the **`docx`** skill.

### How to Use docx Skill

1. **Load the docx skill** when you need to create a .docx file from the generated paper content
2. **The docx skill** handles:
   - Page setup (A4, margins, orientation)
   - Heading styles (Heading 1, 2, 3)
   - Tables (with proper column widths)
   - Numbered and bulleted lists
   - Images and figures
   - Headers/footers and page numbers
   - Table of contents

### Integration Workflow

```
Paper Drafting (this skill) → Generate paper content in JSON/Text
                                        ↓
                               Load docx skill
                                        ↓
                              Create formatted .docx file
```

### Example: Converting Paper to Word Document

When the user requests a Word document output, use the docx skill with the generated content:

```javascript
// Using docx skill's docx-js library
const { Document, Packer, Paragraph, TextRun, HeadingLevel } = require('docx');

const doc = new Document({
  sections: [{
    properties: { page: { size: { width: 11906, height: 16838 }, margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 } } },
    children: [
      // Title
      new Paragraph({ heading: HeadingLevel.HEADING_1, children: [new TextRun(paper.title)]] }),
      // Abstract
      new Paragraph({ heading: HeadingLevel.HEADING_2, children: [new TextRun("Abstract")] }),
      new Paragraph(paper.sections.abstract),
      // ... other sections
    ]
  }]
});

await Packer.toBuffer(doc);
```

### Supported Output Formats

| Format | Description | Use docx Skill? |
|--------|-------------|-----------------|
| Markdown | Plain text with formatting | No - just output text |
| LaTeX | Academic paper format | No - for conferences like NeurIPS |
| Word (.docx) | Professional document | **YES - use docx skill** |
| PDF | Final submission | Convert from docx or LaTeX |

---

## Processing Steps

### Step 1: Load Paper Template

The paper template is defined in the external reference directory. **DO NOT hardcode template filename.**

**Template Directory:**
```
skills/research/research-flow/paper/references/
```

**How to Load Dynamically:**
1. List all `.docx` files in the references directory
2. If multiple templates exist, ask user which to use
3. If only one exists, use that one
4. Read the chosen template using docx skill
5. Parse to understand required sections and format
6. Adjust generated content to match template format

```python
def load_paper_template(user_preferred: str = None) -> PaperTemplate:
    """Load paper template dynamically from references directory."""
    
    ref_dir = "skills/research/research-flow/paper/references/"
    
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
- Paper title format
- Section order and naming
- Figure/table placement
- Reference format
- Page limit

**Note:** Templates are loaded dynamically - filename changes don't require skill updates.

---

## Step 2: Generate Paper Content
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

Before outputting paper draft, verify:

- [ ] All sections follow target venue format
- [ ] Every claim has evidence backing
- [ ] Citations are verifiable (no hallucinations)
- [ ] Consistent terminology throughout
- [ ] No redundant content across sections
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
3. **docx skill** handles all formatting

**⚠️ Chinese papers:** Use **python-docx** (not docx-js). docx-js chokes on Chinese smart quotes in JS strings. See docx skill's `references/chinese-md-to-docx-recipe.md` for the **two-phase table detection** technique — scan for all `|---|` separators first, then process content; do NOT try inline table detection during the main parse loop. It will drop data rows silently.

**⚠️ Environment:** `execute_code` sandbox often lacks `python-docx`. Always run .docx generation scripts via `terminal: python3 script.py`.

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