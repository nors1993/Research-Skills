---
name: research-deep-researcher
title: Deep Research Module
description: "Conduct thorough literature research on any research topic across all academic domains."
version: 1.0.0
author: Sisyphus
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Research, Literature Review, Academic Search, Paper Analysis, Domain-Agnostic, Geography, Remote Sensing, GIS, Geology, Petroleum, Hydrogeology]
    category: research
    related_skills: [research-feasibility-researcher, paper-drafting, research-idea-parser]
    requires_toolsets: [llm, web_search, files]
---

# Deep Research Module

This module conducts thorough literature research on a research topic, collecting papers, analyzing their content, and organizing findings for downstream use in paper writing.

---

## When To Use This Skill

Use this skill when:
- **Need comprehensive literature review** — Find all relevant papers on a topic
- **Deep dive into specific area** — Go beyond surface-level search
- **Build bibliography** — Gather papers for citation
- **Understand research landscape** — Map out the field

---

## Core Philosophy

1. **Iterative breadth-then-depth** — Start broad, then dive deep
2. **Verify every citation** — Never trust AI-generated references
3. **Extract key insights** — Don't just collect, analyze
4. **Build structured knowledge** — Organize for downstream use

---

## Input Format

```json
{
  "research_question": "Can dynamic example selection in chain-of-thought prompting improve reasoning performance?",
  "domain": "NLP",
  "key_concepts": ["chain-of-thought", "few-shot learning", "example selection", "prompt engineering"],
  "feasibility_report": {
    "related_work_summary": {...},
    "novelty_assessment": {...}
  }
}
```

---

## Output Format

```json
{
  "research_question": "original question",
  "literature_review": {
    "sections": [
      {
        "theme": "Theme name",
        "papers": [
          {"title": "...", "year": 2024, "authors": ["..."], "key_contribution": "...", "method": "...", "limitations": "..."}
        ],
        "synthesis": "How these papers relate to your work"
      }
    ]
  },
  "bibliography": {
    "papers": [...],  # Full BibTeX entries
    "bibtex_string": "..."
  },
  "research_gaps": [
    {"gap": "description", "severity": "high | medium | low", "opportunity": "how to address"}
  ],
  "methodology_options": [
    {"method": "...", "pros": "...", "cons": "...", "suitability": "high | medium | low"}
  ]
}
```

---

## Processing Steps

### Step 1: Initial Broad Search

Execute parallel searches across multiple angles:

```python
async def initial_search(brief: ResearchBrief) -> List[Paper]:
    """Execute broad search across different angles."""
    
    # Select appropriate search sources based on domain
    sources = get_domain_search_sources(brief.domain, brief.sub_domain)
    
    # Generate domain-appropriate queries
    search_queries = generate_domain_queries(brief.key_concepts, brief.research_type)
    
    # Search across selected sources
    tasks = [search_source(source, query, limit=10) for source, query in zip(sources, search_queries)]
    results = await asyncio.gather(*tasks)
    return deduplicate(results)

def get_domain_search_sources(domain: str, sub_domain: str = None) -> List[str]:
    """Get appropriate search sources based on domain."""
    sources = {
        "Medical Sciences": ["PubMed", "Cochrane Library", "Google Scholar", "ClinicalTrials.gov"],
        "Natural Sciences": ["Web of Science", "Scopus", "arXiv", "Google Scholar"],
        "Engineering": ["IEEE Xplore", "Scopus", "Google Scholar", "Engineering Village"],
        "Computer Science": ["ACM Digital Library", "arXiv", "Google Scholar", "DBLP"],
        "Social Sciences": ["JSTOR", "Google Scholar", "SSRN", "Sociological Abstracts"],
        "Economics": ["NBER", "SSRN", "RePEc", "Google Scholar", "JSTOR"],
        "Psychology": ["PsycINFO", "Google Scholar", "PubMed", "SSRN"],
        "Humanities": ["JSTOR", "Google Scholar", "PhilPapers", "MLA Bibliography"],
        "Geography & Earth Sciences": ["Web of Science", "GeoRef", "Google Scholar", "JSTOR", "Elsevier GeoBase"],
        "Remote Sensing & GIS": ["IEEE Xplore", "ISPRS Journal", "Remote Sensing", "ScienceDirect", "Google Scholar", "SPIE Digital Library"],
        "Geology": ["GeoRef", "Web of Science", "Google Scholar", "GSL Publications", "Elsevier GeoScience", "Springer GeoSciences"],
        "Oil & Gas Sciences": ["SPE", "SEG", "AAPG", "OnePetro", "Journal of Petroleum Geology", "Google Scholar", "ScienceDirect"],
        "Earth Sciences": ["Web of Science", "GeoRef", "AGU Publications", "Elsevier Earth Sciences", "Google Scholar"]
    }
    return sources.get(domain, ["Google Scholar", "arXiv", "Web of Science"])

def generate_domain_queries(key_concepts: List[str], research_type: str) -> List[str]:
    """Generate domain-appropriate search queries."""
    if not key_concepts:
        return []
    
    queries = [
        # Direct topic search
        " ".join(key_concepts[:2]),
        # With methodology term
        f"{key_concepts[0]} methodology",
        # With problem term
        f"{key_concepts[0]} analysis",
        # Broader search
        f"research {key_concepts[0]}"
    ]
    return queries[:4]  # Return up to 4 queries
```

### Step 2: Citation Graph Expansion

Use citation relationships to find more papers:

```python
def expand_via_citations(seed_papers: List[Paper]) -> List[Paper]:
    """Find more papers through citation relationships."""
    
    expanded = []
    for paper in seed_papers:
        # Get papers that cite this paper (newer work)
        citing = semantic_scholar.get_citing(paper.id)
        
        # Get papers this paper cites (foundational work)
        referenced = semantic_scholar.get_references(paper.id)
        
        expanded.extend(citing + referenced)
    
    return deduplicate(expanded)
```

### Step 3: Deep Paper Analysis

For each relevant paper, extract key information:

```python
def analyze_paper(paper: Paper) -> PaperAnalysis:
    """Analyze a paper and extract key information."""
    
    prompt = f"""
    Analyze the following paper and extract:
    1. Key contribution (one sentence)
    2. Methodology used
    3. Key results
    4. Limitations
    5. How it relates to: {research_question}
    
    Paper: {paper.title}
    Abstract: {paper.abstract}
    
    Provide structured analysis.
    """
    
    return llm.generate(prompt, schema=PaperAnalysis)
```

### Step 4: Organize by Theme

Group papers by research themes:

```python
def organize_by_theme(papers: List[PaperAnalysis]) -> List[ThemeSection]:
    """Organize papers into thematic sections."""
    
    themes = {
        "Chain-of-Thought Methods": [],
        "Example Selection Strategies": [],
        "Few-Shot Learning": [],
        "Reasoning Improvement": []
    }
    
    for paper in papers:
        theme = classify_theme(paper)
        themes[theme].append(paper)
    
    return [
        ThemeSection(name=theme, papers=papers)
        for theme, papers in themes.items() if papers
    ]
```

### Step 5: Identify Research Gaps

Analyze the literature to find gaps:

```python
def identify_gaps(literature: LiteratureReview) -> List[ResearchGap]:
    """Identify gaps in the research landscape."""
    
    prompt = f"""
    Based on the following literature review, identify research gaps:
    1. What problems remain unsolved?
    2. What methods haven't been explored?
    3. What configurations haven't been tested?
    4. What applications haven't been addressed?
    
    Literature: {format_literature(literature)}
    
    Return gap descriptions with severity assessment.
    """
    
    return llm.generate(prompt, schema=List[ResearchGap])
```

### Step 6: Generate BibTeX

Create proper bibliography entries:

```python
def generate_bibtex(papers: List[Paper]) -> str:
    """Generate BibTeX entries for papers."""
    
    bibtex_entries = []
    for paper in papers:
        # Fetch actual BibTeX via DOI/content negotiation
        bibtex = fetch_bibtex(paper.doi)
        bibtex_entries.append(bibtex)
    
    return "\n\n".join(bibtex_entries)
```

---

## Search Strategy Template

### Round 1: Breadth (Parallel)

| Query Type | Example | Goal |
|------------|---------|------|
| Direct | "chain-of-thought example selection" | Find exact matches |
| Method | "few-shot prompting techniques" | Find methods |
| Problem | "LLM reasoning improvement" | Find problem-focused work |
| Baseline | "CoT prompting baselines" | Find comparison points |

### Pitfalls — Source Reliability by Domain

| Source | Best For | Pitfall |
|--------|----------|---------|
| arXiv API | CS, math, physics, econ | Weak for social science and humanities; few relevant papers for topics like food security, urban studies, policy. The `search_arxiv.py` script uses OR logic which returns many false positives. |
| Semantic Scholar API | All domains | **Frequent 429 rate-limiting** on the free tier. If you hit `"Too Many Requests"`, fall back immediately — do not retry. |
| **Crossref API** | All domains — **most reliable** | Returns real, verifiable papers with DOIs. Accepts REST queries: `curl "https://api.crossref.org/works?query=TERMS&rows=15&filter=type:journal-article"`. Parse with `python3 -m json.tool`. **Use this as the primary source for social science, medical, and humanities literature.** |
| Google Scholar (browser) | All domains | Frequently blocked by CAPTCHA/bot detection. Unreliable in headless contexts. |

### Round 2: Depth (Iterative)

```
After Round 1:
- Extract new terminology from found papers
- Identify key authors in the field
- Find papers that cite the most relevant works
- Search for "negative results" in the area

Example follow-up queries:
- "[New term 1]" from Round 1 papers
- "[Author name] chain-of-thought"
- "[Paper title]" citation search
```

### Round 3: Targeted (Specific Gaps)

```
Based on identified gaps:
- Search for specific combinations not explored
- Look for papers addressing the gap
- Find papers with contrary findings
```

---

## Literature Review Structure

### Theme 1: Core Methods

```
### Chain-of-Thought Prompting
Standard CoT establishes the baseline...

Related works:
- [Paper 1]: Introduces X, builds on standard CoT by...
- [Paper 2]: Proposes Y, different approach because...

Our work differs by...
```

### Theme 2: Related Approaches

```
### Example Selection Methods
Prior work on example selection includes...

Our approach differs in...
```

### Theme 3: Applications

```
### Reasoning Tasks
CoT has been applied to...

Our focus on... differs in scope/tasks.
```

---

## Example Output

### Example Literature Review

**Input:** Research question on dynamic example selection in CoT prompting

**Output:**
```json
{
  "research_question": "Can dynamic example selection in chain-of-thought prompting improve reasoning performance?",
  "literature_review": {
    "sections": [
      {
        "theme": "Chain-of-Thought Methods",
        "papers": [
          {
            "title": "Chain-of-Thought Prompting Elicits Reasoning in LLMs",
            "year": 2022,
            "authors": ["Wei et al."],
            "key_contribution": "Shows that adding 'let's think step by step' enables reasoning",
            "method": "Manual prompting with reasoning traces",
            "limitations": "Requires hand-crafted examples"
          },
          {
            "title": "Self-Consistency Improves CoT",
            "year": 2022,
            "authors": ["Wang et al."],
            "key_contribution": "Uses multiple samples and majority voting",
            "method": "Sampling + voting",
            "limitations": "Doesn't address example selection"
          }
        ],
        "synthesis": "Standard CoT relies on fixed examples; self-consistency adds sampling but doesn't optimize example selection"
      },
      {
        "theme": "Example Selection",
        "papers": [
          {
            "title": "Active Prompting",
            "year": 2023,
            "authors": ["Diao et al."],
            "key_contribution": "Selects prompts based on uncertainty",
            "method": "Uncertainty-based selection",
            "limitations": "Selects prompts not examples"
          }
        ],
        "synthesis": "No prior work specifically addresses similarity-based example selection for CoT"
      }
    ]
  },
  "bibliography": {
    "papers": [...],
    "@article{wei2022chain,
      author = {Wei, Jason and et al.},
      title = {Chain-of-Thought Prompting Elicits Reasoning in LLMs},
      year = {2022},
      ...}"
  },
  "research_gaps": [
    {
      "gap": "No prior work on similarity-based dynamic example selection for CoT",
      "severity": "low",
      "opportunity": "This is the novel contribution of our work"
    },
    {
      "gap": "Limited evaluation on diverse reasoning tasks",
      "severity": "medium",
      "opportunity": "We should evaluate on varied benchmarks"
    }
  ],
  "methodology_options": [
    {
      "method": "Embedding-based similarity selection",
      "pros": "Scalable, interpretable",
      "cons": "Requires embedding model",
      "suitability": "high"
    },
    {
      "method": "LLM-based selection",
      "pros": "Can capture semantic nuances",
      "cons": "Expensive, slower",
      "suitability": "medium"
    }
  ]
}
```

---

## Quality Checklist

Before outputting literature review, verify:

- [ ] Searched at least 3 rounds with different queries
- [ ] Reviewed at least 15-20 papers
- [ ] All citations are verifiable (not hallucinated)
- [ ] Organized by themes, not just listed
- [ ] Identified clear research gaps
- [ ] Generated valid BibTeX entries

---

## Integration with Downstream Modules

The deep research output is used by:

1. **Paper Drafting** — Uses `literature_review` and `bibliography` for related work section
2. **Consistency Checker** — Uses `research_gaps` to verify paper addresses them
3. **Template Formatter** — Uses `methodology_options` for methods section

---

## Tool Requirements

| Tool | Purpose | Reliability Note |
|------|---------|-----------------|
| **Crossref API** | Find papers by topic, author, title; returns verified DOIs | **MOST RELIABLE for social sciences.** Free, no rate limit issues observed. Use `curl -s "https://api.crossref.org/works?query=TERMS&rows=N&filter=type:journal-article"` |
| ArXiv Search | Find recent papers | Good for CS/physics/math; **poor for social sciences and humanities.** Most food security, development studies, and urban planning literature is in journals, not arXiv. |
| Semantic Scholar API | Citation graph, paper details | **Frequently rate-limited (429 errors).** Try Crossref first; use S2 only for citation-specific needs. |
| Google Scholar (web) | Broad academic search | **Bot detection aggressive.** Often blocked; use as last resort via browser. |
| Web Search | Find blog posts, preprints | Supplementary only; not reliable for primary literature verification. |
| DOI → BibTeX | Generate citations | Use Crossref content negotiation: `curl -LH "Accept: application/x-bibtex" https://doi.org/DOI` |

### Pitfall: Search source mismatch by domain

Social science and humanities topics (food security, development studies, urban planning, public health) are **poorly covered by arXiv** but richly covered by journal databases indexed in Crossref. When searching for papers in these domains:
1. **Start with Crossref API** — it indexes journals where social science literature lives
2. **Verify key papers by author+title** — use specific queries like `?query=Crush+Frayne+urban+food+insecurity` rather than broad topic searches
3. **Don't rely on arXiv** for social science literature — it will return mostly irrelevant CS papers that happen to share keywords
4. **Semantic Scholar is supplementary** — use for citation counts and related-paper discovery, not as a primary search source