---
name: research-idea-parser
title: Research Idea Parser
description: "Parse user research ideas into structured research briefs across all academic domains."
version: 1.1.0
author: Sisyphus
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Research, Idea Parsing, Structured Output, Domain-Agnostic, Geography, Remote Sensing, GIS, Geology, Petroleum, Hydrogeology]
    category: research
    related_skills: [feasibility-researcher, deep-researcher, paper-drafting]
    requires_toolsets: [llm, files]
---

# Research Idea Parser

This skill parses user-provided research ideas into structured research briefs that can be used by downstream modules. It is **domain-agnostic** and works for any academic field: natural sciences, engineering, medicine, social sciences, humanities, etc.

---

## When To Use This Skill

Use this skill when:
- **User provides a raw research idea** — needs to be extracted and structured
- **Need to identify research domain** — classify into appropriate academic field
- **Need to extract key concepts** — identify core terminology and concepts
- **Need to validate idea quality** — check if idea is sufficient for research
- **Any discipline** — physics, biology, chemistry, medicine, economics, psychology, sociology, engineering, etc.

---

## Core Philosophy

1. **Extract, don't interpret** — Capture what the user said, not what you think they meant
2. **Structure everything** — Convert informal ideas into formal research brief format
3. **Preserve ambiguity** — If user's idea is unclear, mark it as such rather than guessing
4. **Identify gaps** — Flag missing information that would be needed for research
5. **Domain-agnostic** — Apply the same parsing logic regardless of field

---

## Input Format

The user typically provides ideas in natural language from any field:

**Example - Medical Research:**
```
"I want to investigate whether a specific biomarker combination can predict
patient response to a new immunotherapy treatment in melanoma patients."
```

**Example - Environmental Science:**
```
"How does microplastic concentration in freshwater systems affect
benthic invertebrate communities over different time scales?"
```

**Example - Economics:**
```
"What is the relationship between remote work adoption and
urban housing prices in post-pandemic markets?"
```

**Example - Engineering:**
```
"Can we develop a self-healing material that automatically repairs
cracks in concrete structures without external intervention?"
```

**Example - Geography:**
```
"What is the relationship between urbanization and land use change
in the Pearl River Delta region from 1990 to 2020, and how did this
affect the spatial pattern of ecosystem services?"
```

**Example - Remote Sensing:**
```
"Can we use multi-source satellite remote sensing data (Landsat, Sentinel-2, 
MODIS) to monitor vegetation drought stress at regional scales, and 
what is the optimal combination of vegetation indices for drought detection?"
```

**Example - GIS & Geoinformatics:**
```
"How can we integrate real-time traffic data with environmental monitoring
to assess the impact of traffic pollution on air quality in urban areas?"
```

**Example - Petroleum Geology:**
```
"What is the relationship between stratigraphic architecture and 
hydrocarbon accumulation in deltaic reservoirs? A case study of 
the Upper Triassic Yanchang Formation in the Ordos Basin."
```

**Example - Reservoir Engineering:**
```
"How can machine learning methods improve the prediction of 
water-cut behavior in heterogeneous sandstone reservoirs, 
and what are the key factors affecting prediction accuracy?"
```

**Example - Structural Geology:**
```
"How did the Cenozoic tectonic evolution of the Qaidam Basin 
affect the formation and distribution of structural traps 
for hydrocarbon accumulation?"
```

**Example - Hydrogeology:**
```
"What is the impact of groundwater overdraft on the hydrochemical 
characteristics and quality of shallow aquifers in the North China Plain?"
```

**Example - Sedimentology:**
```
"What are the provenance and depositional systems of deep-water 
fan deposits in the Cretaceous Lg= Formation, and their implications 
for reservoir quality?"
```

---

## Output Format

The parser outputs a structured research brief in JSON format:

```json
{
  "raw_idea": "original user input",
  "research_question": "one clear research question",
  "domain": "Medicine | Biology | Chemistry | Physics | Engineering | Economics | Psychology | Sociology | Computer Science | etc.",
  "sub_domain": "specific sub-field if applicable",
  "key_concepts": ["concept1", "concept2", "..."],
  "potential_methods": ["method1", "method2", "..."],
  "research_type": "empirical | theoretical | applied | mixed",
  "intended_application": "what this research would be used for",
  "confidence": "high | medium | low",
  "clarification_needed": ["question1", "question2", "..."],
  "suggested_search_terms": ["term1", "term2", "..."]
}
```

---

## Processing Steps

### Step 1: Extract Core Research Question

Analyze the user's input and extract a single, clear research question.

**If multiple ideas are present**, ask for clarification or split into multiple briefs.

**If idea is too vague**, note what clarification is needed.

```python
def extract_research_question(idea: str) -> str:
    """Extract the core research question from user input."""
    prompt = f"""
    Given the following research idea, extract the core research question.
    - State it as a question that could be answered through research
    - Be specific about what is being investigated
    - Avoid vague terms like "improve" without specifying what

    Input: {idea}

    Research Question:
    """
    return llm.generate(prompt)
```

### Step 2: Classify Domain

Determine the primary academic/research domain. This should cover all major fields:

| Domain | Sub-domains | Key Indicators |
|--------|-------------|----------------|
| **Natural Sciences** | Physics, Chemistry, Biology, Geology, Astronomy | experiments, observations, natural phenomena |
| **Medical Sciences** | Clinical, Public Health, Pharmacy, Nursing | patients, treatments, biomarkers, clinical trials |
| **Engineering** | Mechanical, Electrical, Civil, Chemical, Software | design, systems, optimization, prototypes |
| **Computer Science** | AI, Systems, Theory, Security, HCI | algorithms, software, computing, data |
| **Life Sciences** | Microbiology, Ecology, Genetics, Neuroscience | organisms, ecosystems, genes, brain |
| **Social Sciences** | Psychology, Sociology, Economics, Political Science | human behavior, society, markets, policy |
| **Humanities** | History, Philosophy, Literature, Languages | texts, culture, history, interpretation |
| **Mathematics** | Pure Math, Applied Math, Statistics | proofs, models, analysis |
| **Geography & Earth Sciences** | Physical Geography, Human Geography, Geoinformatics | spatial analysis, GIS, terrain, land use |
| **Remote Sensing & GIS** | Remote Sensing, Photogrammetry, GIS, Geodesy | satellite imagery, aerial photography, spatial data |
| **Geology** | Structural Geology, Sedimentology, Petrology, Paleontology, Hydrogeology | rock formations, geological structures, fossils, groundwater |
| **Oil & Gas Sciences** | Petroleum Geology, Reservoir Engineering, Geophysics, Drilling Engineering | hydrocarbon accumulation, reservoir characterization, seismic exploration |
| **Earth Sciences** | Oceanography, Atmospheric Science, Glaciology, Geochemistry | oceans, atmosphere, glaciers, geochemical processes |

### Step 3: Determine Research Type

Identify what kind of research this is:

| Research Type | Description | Typical Methods |
|---------------|-------------|-----------------|
| **Empirical** | Based on observation or experiment | experiments, surveys, case studies, field work |
| **Theoretical** | Based on mathematical/logical derivation | proofs, modeling, framework development |
| **Applied** | Problem-solving for practical use | design, development, implementation |
| **Mixed** | Combines multiple approaches | varies by combination |

### Step 4: Extract Key Concepts

Identify 3-7 key concepts that define the research area.

```python
def extract_key_concepts(idea: str) -> List[str]:
    """Extract key research concepts."""
    prompt = f"""
    Extract 3-7 key concepts from this research idea.
    Each concept should be a term that would be useful for searching literature.

    Ideas: {idea}

    Key Concepts (comma-separated):
    """
    return [c.strip() for c in llm.generate(prompt).split(",")]
```

### Step 5: Identify Potential Methods

Suggest potential research methods based on the idea and domain:

| Research Type | Potential Methods |
|---------------|-------------------|
| Empirical - Lab | controlled experiments, measurements, statistical analysis |
| Empirical - Field | observational study, sampling, surveys, case studies |
| Theoretical | mathematical proof, analytical modeling, simulation |
| Applied | prototype development, system design, optimization |
| Clinical | randomized controlled trial, cohort study, case-control |

### Step 6: Assess Confidence

Rate confidence in the parsed brief:

- **High**: Clear idea, well-specified research question, clear methodology
- **Medium**: Some ambiguity, reasonable assumptions made
- **Low**: Major clarifications needed, unclear direction

### Step 7: Generate Clarification Questions

If confidence is not high, generate questions to clarify the idea. Examples:

- "What specific population/sample will you study?"
- "What is the expected outcome or hypothesis?"
- "What data sources or experimental setups are available?"
- "What is the timeline and resource constraints?"
- "Who is the target audience for this research?"

---

## Example Outputs

### Example 1: Medical Research (Well-specified)

**Input:**
```
"I want to investigate whether a specific biomarker combination (PD-L1 expression
and TMB levels) can predict patient response to pembrolizumab immunotherapy
in advanced melanoma patients who have failed first-line treatment."
```

**Output:**
```json
{
  "raw_idea": "I want to investigate whether a specific biomarker combination (PD-L1 expression and TMB levels) can predict patient response to pembrolizumab immunotherapy in advanced melanoma patients who have failed first-line treatment.",
  "research_question": "Can the combination of PD-L1 expression and tumor mutational burden (TMB) levels predict response to pembrolizumab in advanced melanoma patients who have progressed on first-line treatment?",
  "domain": "Medical Sciences",
  "sub_domain": "Oncology, Immunotherapy",
  "key_concepts": ["PD-L1", "tumor mutational burden", "pembrolizumab", "melanoma", "biomarker", "immunotherapy response", "predictive markers"],
  "potential_methods": ["retrospective cohort study", "multivariate analysis", "ROC curve analysis", "survival analysis"],
  "research_type": "empirical",
  "intended_application": "Improve patient selection for immunotherapy treatment",
  "confidence": "high",
  "clarification_needed": [],
  "suggested_search_terms": ["PD-L1 TMB melanoma pembrolizumab", "immunotherapy biomarker predictive", "advanced melanoma second-line treatment"]
}
```

### Example 2: Environmental Science (Well-specified)

**Input:**
```
"How does increasing microplastic concentration in freshwater rivers affect
the diversity and abundance of benthic invertebrate communities, and does
this effect vary across different river types (urban vs agricultural)?"
```

**Output:**
```json
{
  "raw_idea": "How does increasing microplastic concentration in freshwater rivers affect the diversity and abundance of benthic invertebrate communities, and does this effect vary across different river types (urban vs agricultural)?",
  "research_question": "What is the relationship between microplastic concentration and benthic invertebrate community structure in freshwater rivers, and how does this relationship differ between urban and agricultural river types?",
  "domain": "Environmental Science",
  "sub_domain": "Ecology, Pollution",
  "key_concepts": ["microplastics", "freshwater ecology", "benthic invertebrates", "bioindicators", "river ecosystems", "pollution effects", "species diversity"],
  "potential_methods": ["field sampling", "community analysis", "comparative study", "multivariate statistics"],
  "research_type": "empirical",
  "intended_application": "Assess ecological impact of microplastic pollution",
  "confidence": "high",
  "clarification_needed": [],
  "suggested_search_terms": ["microplastic benthic invertebrates freshwater", "river pollution ecological impact", "urban vs agricultural stream ecology"]
}
```

### Example 3: Economics (Well-specified)

**Input:**
```
"What is the effect of widespread remote work adoption on housing prices
in major metropolitan areas between 2020 and 2024, and how does this
differ between cities with varying levels of tech industry concentration?"
```

**Output:**
```json
{
  "raw_idea": "What is the effect of widespread remote work adoption on housing prices in major metropolitan areas between 2020 and 2024, and how does this differ between cities with varying levels of tech industry concentration?",
  "research_question": "How did remote work adoption affect housing price changes in major metropolitan areas from 2020-2024, and does this effect vary by city tech industry concentration?",
  "domain": "Economics",
  "sub_domain": "Urban Economics, Housing Economics",
  "key_concepts": ["remote work", "housing prices", "urban economics", "tech industry", "metropolitan areas", "panel data analysis", "difference-in-differences"],
  "potential_methods": ["econometric analysis", "panel data regression", "difference-in-differences", "hedonic pricing model"],
  "research_type": "empirical",
  "intended_application": "Inform urban planning and housing policy",
  "confidence": "high",
  "clarification_needed": [],
  "suggested_search_terms": ["remote work housing prices metropolitan", "tech industry housing demand", "post-pandemic urban housing market"]
}
```

### Example 4: Engineering (Vague)

**Input:**
```
"Make materials stronger"
```

**Output:**
```json
{
  "raw_idea": "Make materials stronger",
  "research_question": null,
  "domain": "Engineering",
  "sub_domain": "Materials Science",
  "key_concepts": ["material strength"],
  "potential_methods": null,
  "research_type": null,
  "intended_application": null,
  "confidence": "low",
  "clarification_needed": [
    "What specific material are you targeting? (metals, polymers, ceramics, composites, etc.)",
    "What type of strength are you focused on? (tensile, compressive, fatigue, impact, etc.)",
    "What is the application context? (structural, aerospace, biomedical, etc.)",
    "Are you looking at improving existing materials or developing new ones?",
    "What would success look like quantitatively?"
  ],
  "suggested_search_terms": []
}
```

---

## Integration with Downstream Modules

The structured brief generated by this skill is used by:

1. **Feasibility Researcher** — Uses `research_question`, `key_concepts`, `suggested_search_terms`, `domain` to find related work
2. **Deep Researcher** — Uses `domain`, `sub_domain`, `key_concepts`, `research_type` to guide literature search
3. **Paper Drafting** — Uses `research_question`, `intended_application`, `research_type` to frame the paper

---

## Quality Checklist

Before outputting the research brief, verify:

- [ ] Research question is clear and specific
- [ ] Domain classification is appropriate for the field
- [ ] Sub-domain identified if applicable
- [ ] Research type determined
- [ ] Key concepts cover the main areas
- [ ] Confidence rating reflects actual certainty
- [ ] Clarification questions are specific and helpful
- [ ] Search terms would return relevant results in the field

---

## Error Handling

| Error | Handling |
|-------|----------|
| Empty input | Ask user to provide a research idea |
| Multiple unrelated ideas | Split into multiple briefs or ask user to prioritize |
| Domain unclear | Mark as "Unknown" and note in clarification |
| Offensive/harmful content | Refuse and explain why |
|跨学科领域 | 创建多个简报或询问用户主要关注领域 |

---

## Prompt Template

```python
RESEARCH_IDEA_PARSER_PROMPT = """
You are a domain-agnostic research idea parser. Your job is to convert informal 
research ideas into structured research briefs that can be used by downstream 
research modules. You must be able to handle ANY academic discipline.

## Input
The user provides a research idea in natural language. It may be:
- A fully specified research plan
- A vague research direction
- A single question
- Multiple ideas mixed together
- From ANY field: physics, biology, medicine, engineering, economics, etc.

## Your Task

1. **Extract the core research question** - State what specifically would be investigated
2. **Classify the domain** - Primary academic field (use broad categories)
3. **Identify sub-domain** - Specific area within the field if applicable
4. **Determine research type** - empirical, theoretical, applied, or mixed
5. **Identify key concepts** - 3-7 terms useful for literature search in THIS field
6. **Suggest potential methods** - How this research could be conducted
7. **Assess confidence** - How well-specified is this idea?
8. **Generate clarification questions** - What's missing that would help?

## Output Format
Return a JSON object with:
- research_question: string or null
- domain: string (broad academic field)
- sub_domain: string or null (specific area)
- key_concepts: array of strings
- potential_methods: array of strings or null
- research_type: "empirical" | "theoretical" | "applied" | "mixed" | null
- intended_application: string or null
- confidence: "high" | "medium" | "low"
- clarification_needed: array of strings
- suggested_search_terms: array of strings

## Rules
- If the idea is too vague, set confidence to "low" and list clarification questions
- If multiple unrelated ideas are present, note this and ask for clarification
- Preserve the user's original intent - don't add your own interpretation
- If you cannot determine something, set it to null rather than guessing
- Use domain-appropriate terminology in your output
"""
```