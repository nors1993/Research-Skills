---
name: research-feasibility-researcher
title: Research Feasibility Researcher
description: "Assess research idea feasibility across any academic domain by finding related work and evaluating novelty."
version: 1.0.0
author: Sisyphus
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Research, Feasibility, Literature Survey, Novelty Assessment, Domain-Agnostic, Geography, Remote Sensing, GIS, Geology, Petroleum, Hydrogeology]
    category: research
    related_skills: [research-idea-parser, deep-researcher, paper-drafting]
    requires_toolsets: [llm, web_search, files]
---

# Research Feasibility Researcher

This module assesses whether a research idea is feasible by searching for related work, evaluating novelty, and determining if there's sufficient room for contribution.

---

## When To Use This Skill

Use this skill when:
- **Need to validate a research idea** — Check if similar work already exists
- **Assess novelty** — Determine if the idea offers something new
- **Find related baselines** — Identify what to compare against
- **Early-stage filtering** — Decide if an idea is worth pursuing

---

## Core Philosophy

1. **Search first, judge second** — Gather evidence before assessing feasibility
2. **Be exhaustive but efficient** — Use multiple sources but don't over-search
3. **Distinguish between similar and identical** — Similar work isn't blocking
4. **Assess the gap** — Not "is there work?" but "is there room?"

---

## Input Format

This module takes the structured brief from `research-idea-parser`:

```json
{
  "research_question": "Can dynamic example selection in chain-of-thought prompting improve reasoning performance?",
  "domain": "NLP",
  "key_concepts": ["chain-of-thought", "few-shot learning", "example selection"],
  "suggested_search_terms": ["chain-of-thought prompting", "dynamic example selection"]
}
```

---

## Output Format

```json
{
  "research_question": "original question",
  "feasibility_score": "high | medium | low",
  "related_work_summary": {
    "papers": [
      {"title": "...", "year": 2024, "relevance": "high | medium | low", "key_insight": "..."}
    ],
    "github_repos": [
      {"name": "...", "stars": 123, "relevance": "high | medium", "description": "..."}
    ]
  },
  "novelty_assessment": {
    "innovation_points": ["point1", "point2"],
    "differentiation_from_existing": "how this differs",
    "risk_of_collision": "low | medium | high"
  },
  "recommendation": "proceed | pivot | abandon",
  "suggested_research_direction": "refined direction if needed"
}
```

---

## Processing Steps

### Step 1: Multi-source Search

Execute parallel searches across multiple sources:

```python
async def search_related_work(brief: ResearchBrief) -> SearchResults:
    """Search multiple sources in parallel."""
    tasks = [
        search_arxiv(brief.key_concepts, limit=10),
        search_github(brief.key_concepts, limit=10),
        search_web(brief.suggested_search_terms, limit=10),
        search_semantic_scholar(brief.research_question, limit=10)
    ]
    results = await asyncio.gather(*tasks)
    return aggregate_results(results)
```

### Step 2: Categorize Results

| Category | Description |
|----------|-------------|
| **Identical** | Same exact problem/method — blocks the idea |
| **Highly Similar** | Very close, need to differentiate significantly |
| **Related** | Similar domain, different approach — opportunity |
| **Different** | Unrelated — no conflict |

### Step 3: Novelty Assessment

**Check for innovation points:**

```python
def assess_novelty(related_work: List[Work], brief: ResearchBrief) -> NoveltyAssessment:
    """Assess novelty of the research idea."""
    
    # Key questions:
    # 1. Does any paper address the exact same question?
    # 2. Does any repo implement the exact same method?
    # 3. Are there papers with similar but not identical approaches?
    # 4. What's the gap between existing work and this idea?
    
    innovation_points = []
    
    # If nothing similar exists → high novelty
    if not related_work:
        innovation_points.append("First work in this specific direction")
    
    # If similar exists but different aspect → moderate novelty
    for work in related_work:
        if work.method != brief.method:
            innovation_points.append(f"Different from {work.title} in aspect X")
    
    # Assess risk of collision with ongoing work
    risk = "high" if any(w.year >= current_year - 1) else "low"
    
    return NoveltyAssessment(
        innovation_points=innovation_points,
        differentiation=...,
        risk_of_collision=risk
    )
```

### Step 4: Feasibility Scoring

| Score | Criteria |
|-------|----------|
| **High** | Clear novelty, no identical work, feasible approach |
| **Medium** | Some related work but differentiation possible |
| **Low** | Very similar existing work, or infeasible approach |

### Step 5: Recommendation Generation

```python
def generate_recommendation(assessment: NoveltyAssessment, related_work: List[Work]) -> Recommendation:
    """Generate recommendation based on assessment."""
    
    if assessment.risk_of_collision == "high":
        return Recommendation(
            action="pivot",
            reason="Very recent similar work suggests collision risk"
        )
    
    if len([w for w in related_work if w.relevance == "high"]) >= 5:
        return Recommendation(
            action="pivot",
            reason="Many highly relevant works - need clearer differentiation"
        )
    
    if assessment.novelty_points:
        return Recommendation(
            action="proceed",
            reason=f"Novel contribution: {assessment.novelty_points[0]}"
        )
    
    return Recommendation(
        action="medium",
        reason="Some related work, but potential for differentiation exists"
    )
```

---

## Search Strategies by Domain

### Medical/Health Sciences
```python
search_queries = [
    "biomarker combination prediction",
    "clinical trial outcome",
    "treatment response predictors"
]
sources = ["PubMed", "ClinicalTrials.gov", "Google Scholar"]
```

### Natural Sciences (Physics, Chemistry, Biology)
```python
search_queries = [
    "novel material properties",
    "protein interaction mechanism",
    "quantum system behavior"
]
sources = ["Web of Science", "arXiv", "Google Scholar"]
```

### Engineering
```python
search_queries = [
    "system optimization method",
    "novel design approach",
    "performance improvement"
]
sources = ["IEEE Xplore", "Google Scholar", "Scopus"]
```

### Social Sciences
```python
search_queries = [
    "behavioral pattern analysis",
    "social interaction research",
    "policy impact assessment"
]
sources = ["JSTOR", "Google Scholar", "SSRN"]
```

### Geography & Earth Sciences
```python
search_queries = [
    "land use change spatial analysis",
    "urban sprawl remote sensing",
    "climate change impact assessment",
    "ecosystem services valuation"
]
sources = ["Web of Science", "Google Scholar", "JSTOR", "Elsevier GeoBase"]
```

### Remote Sensing & GIS
```python
search_queries = [
    "satellite image classification deep learning",
    "multi-temporal remote sensing change detection",
    "LiDAR point cloud processing",
    "GPS trajectory analysis"
]
sources = ["IEEE Xplore", "ISPRS Journal", "Remote Sensing", "Google Scholar", "ScienceDirect"]
```

### Geology
```python
search_queries = [
    "structural geology fault analysis",
    "sedimentology depositional systems",
    "paleontology fossil assemblages",
    "hydrogeology groundwater modeling"
]
sources = ["Web of Science", "GeoRef", "Google Scholar", "Elsevier GeoScience", "GSL journals"]
```

### Oil & Gas Sciences
```python
search_queries = [
    "petroleum geology reservoir characterization",
    "seismic interpretation structural geology",
    "reservoir engineering simulation",
    "basin modeling hydrocarbon migration"
]
sources = ["SPE", "SEG", "AAPG", "Google Scholar", "OnePetro", "Journal of Petroleum Geology"]
```

### General (Domain-Agnostic)
```python
search_queries = [
    f"{key_concept[0]} {key_concept[1]}",
    f"{key_concept[0]} systematic review",
    f"{key_concept[0]} methodology"
]
sources = ["Google Scholar", "Web of Science", "arXiv"]
```

---

## Example Output

### Example: Feasible Idea

**Input Brief:**
```json
{
  "research_question": "Can dynamic example selection in chain-of-thought prompting improve reasoning performance?",
  "key_concepts": ["chain-of-thought", "few-shot learning", "example selection"],
  "suggested_search_terms": ["chain-of-thought prompting", "dynamic example selection"]
}
```

**Output:**
```json
{
  "research_question": "Can dynamic example selection in chain-of-thought prompting improve reasoning performance?",
  "feasibility_score": "high",
  "related_work_summary": {
    "papers": [
      {"title": "Self-Consistency Improves Chain-of-Thought Reasoning", "year": 2022, "relevance": "related", "key_insight": "Different approach - uses multiple samples not example selection"},
      {"title": "Least-to-Most Prompting", "year": 2022, "relevance": "related", "key_insight": "Decomposition approach, not example selection"},
      {"title": "Active Prompting", "year": 2023, "relevance": "related", "key_insight": "Selects prompts based on uncertainty, not examples"}
    ],
    "github_repos": []
  },
  "novelty_assessment": {
    "innovation_points": [
      "First to apply dynamic example selection specifically to CoT",
      "Novel similarity-based selection mechanism"
    ],
    "differentiation_from_existing": "Unlike self-consistency (multiple samples) and active prompting (uncertainty-based), this uses input-example similarity for selection",
    "risk_of_collision": "low"
  },
  "recommendation": "proceed",
  "suggested_research_direction": "Implement similarity-based selection and benchmark against fixed selection baselines"
}
```

### Example: Low Feasibility

**Input Brief:**
```json
{
  "research_question": "Can adding more parameters improve LLM performance?",
  "key_concepts": ["LLM", "parameters", "scaling"],
  "suggested_search_terms": ["LLM scaling", "model size performance"]
}
```

**Output:**
```json
{
  "research_question": "Can adding more parameters improve LLM performance?",
  "feasibility_score": "low",
  "related_work_summary": {
    "papers": [
      {"title": "Chinchilla", "year": 2022, "relevance": "high", "key_insight": "Shows optimal scaling laws"},
      {"title": "Scaling Laws", "year": 2020, "relevance": "high", "key_insight": "Foundational scaling laws"},
      {"title": "Emergent Abilities", "year": 2022, "relevance": "high", "key_insight": "Documents emergent scaling properties"}
    ],
    "github_repos": []
  },
  "novelty_assessment": {
    "innovation_points": [],
    "differentiation_from_existing": "This is the core research question of LLM scaling - extensively studied",
    "risk_of_collision": "high"
  },
  "recommendation": "abandon",
  "suggested_research_direction": "Try a more specific aspect: 'Can dynamic scaling during inference improve LLM performance?' or 'Can smaller models be prompted to mimic larger models through specific techniques?'"
}
```

### Example: Remote Sensing - High Feasibility

**Input Brief:**
```json
{
  "research_question": "Can we use multi-source satellite data (Landsat-8, Sentinel-2) to improve urban land cover classification accuracy compared to single-source approaches?",
  "domain": "Remote Sensing & GIS",
  "sub_domain": "Remote Sensing Image Classification",
  "key_concepts": ["multi-source remote sensing", "urban land cover", "image classification", "data fusion"],
  "research_type": "empirical"
}
```

**Output:**
```json
{
  "research_question": "Can we use multi-source satellite data (Landsat-8, Sentinel-2) to improve urban land cover classification accuracy compared to single-source approaches?",
  "domain": "Remote Sensing & GIS",
  "feasibility_score": "high",
  "related_work_summary": {
    "papers": [
      {"title": "Fusion of Landsat-8 and Sentinel-2 for Land Cover Classification", "year": 2023, "relevance": "highly related", "key_insight": "Shows improved accuracy but limited to specific study area"},
      {"title": "Deep Learning for Multi-source Satellite Image Fusion", "year": 2022, "relevance": "related", "key_insight": "Uses CNN for fusion but focuses on pan-sharpening not classification"},
      {"title": "Urban Land Cover Classification: A Review", "year": 2021, "relevance": "related", "key_insight": "Reviews methods but doesn't address multi-source fusion systematically"}
    ],
    "datasets": [
      {"name": "Landsat-8 Collection 2", "source": "USGS", "relevance": "high"},
      {"name": "Sentinel-2 L2A", "source": "ESA", "relevance": "high"},
      {"name": "Urban Atlas 2018", "source": "EEA", "relevance": "medium"}
    ],
    "ongoing_research": []
  },
  "novelty_assessment": {
    "innovation_points": [
      "Systematic comparison of fusion strategies for urban classification",
      "Novel feature-level fusion approach combining spectral and texture features"
    ],
    "differentiation_from_existing": "Unlike prior work that focuses on specific fusion techniques or small study areas, this proposes a generalized framework applicable to different urban contexts",
    "risk_of_collision": "low"
  },
  "feasibility_factors": {
    "data_availability": "available",
    "methodological_complexity": "medium",
    "resource_requirements": "medium",
    "ethical_considerations": "none"
  },
  "recommendation": "proceed",
  "suggested_research_direction": "Proceed with systematic framework development using open-access data"
}
```

### Example: Geography - Medium Feasibility

**Input Brief:**
```json
{
  "research_question": "What is the impact of climate change on vegetation phenology in the Tibetan Plateau from 1982 to 2020?",
  "domain": "Geography & Earth Sciences",
  "sub_domain": "Climate Change Impact",
  "key_concepts": ["climate change", "vegetation phenology", "NDVI", "Tibetan Plateau", "time series analysis"],
  "research_type": "empirical"
}
```

**Output:**
```json
{
  "research_question": "What is the impact of climate change on vegetation phenology in the Tibetan Plateau from 1982 to 2020?",
  "domain": "Geography & Earth Sciences",
  "feasibility_score": "medium",
  "related_work_summary": {
    "papers": [
      {"title": "Climate Change Impacts on Tibetan Plateau Vegetation", "year": 2022, "relevance": "highly related", "key_insight": "Documents greening trend but limited time period"},
      {"title": "NDVI Trends over the Tibetan Plateau", "year": 2020, "relevance": "highly related", "key_insight": "Long-term analysis but doesn't link to climate explicitly"},
      {"title": "Phenology Changes in High Mountain Asia", "year": 2021, "relevance": "related", "key_insight": "Regional study with different methodology"}
    ],
    "datasets": [
      {"name": "GIMMS NDVI", "source": "NASA", "relevance": "high"},
      {"name": "MODIS NDVI", "source": "NASA", "relevance": "high"},
      {"name": "CRU Climate Data", "source": "UEA", "relevance": "high"}
    ],
    "ongoing_research": []
  },
  "novelty_assessment": {
    "innovation_points": [
      "Longer time series (38 years) with consistent methodology",
      "Explicit linkage between climate variables and phenological metrics"
    ],
    "differentiation_from_existing": "Extends prior work with longer time series and more rigorous climate-phenology correlation",
    "risk_of_collision": "low"
  },
  "feasibility_factors": {
    "data_availability": "available",
    "methodological_complexity": "medium",
    "resource_requirements": "high",
    "ethical_considerations": "none"
  },
  "recommendation": "proceed",
  "suggested_research_direction": "Proceed with attention to data quality control for long time series. Consider GIMMS vs MODIS consistency issues."
}
```

### Example: Petroleum Geology - High Feasibility

**Input Brief:**
```json
{
  "research_question": "What is the relationship between stratigraphic architecture and hydrocarbon accumulation in deltaic reservoirs? A case study of the Upper Triassic Yanchang Formation in the Ordos Basin.",
  "domain": "Oil & Gas Sciences",
  "sub_domain": "Petroleum Geology",
  "key_concepts": ["stratigraphic architecture", "deltaic reservoir", "hydrocarbon accumulation", "sedimentary facies", "Yanchang Formation"],
  "research_type": "empirical"
}
```

**Output:**
```json
{
  "research_question": "What is the relationship between stratigraphic architecture and hydrocarbon accumulation in deltaic reservoirs?",
  "domain": "Oil & Gas Sciences",
  "feasibility_score": "high",
  "related_work_summary": {
    "papers": [
      {"title": "Deltaic Reservoir Architecture and Hydrocarbon Distribution in Ordos Basin", "year": 2022, "relevance": "highly related", "key_insight": "Documents deltaic systems but limited to specific area"},
      {"title": "Sequence Stratigraphy of Upper Triassic Yanchang Formation", "year": 2020, "relevance": "highly related", "key_insight": "Establishes sequence framework but doesn't link to accumulation"},
      {"title": "Sedimentary Facies and Reservoir Quality of Deltaic Sandstones", "year": 2021, "relevance": "related", "key_insight": "Focuses on petrophysics not spatial distribution"}
    ],
    "datasets": [
      {"name": "Seismic data", "source": "CNPC", "relevance": "high"},
      {"name": "Well logs", "source": "Ordos Basin", "relevance": "high"},
      {"name": "Core data", "source": "Field reports", "relevance": "high"}
    ],
    "ongoing_research": []
  },
  "novelty_assessment": {
    "innovation_points": [
      "Links detailed stratigraphic architecture to hydrocarbon distribution patterns",
      "Novel quantitative approach to deltaic reservoir characterization"
    ],
    "differentiation_from_existing": "Unlike prior work that studies stratigraphy or reservoir quality separately, this integrates both with hydrocarbon accumulation",
    "risk_of_collision": "low"
  },
  "feasibility_factors": {
    "data_availability": "available",
    "methodological_complexity": "high",
    "resource_requirements": "high",
    "ethical_considerations": "none"
  },
  "recommendation": "proceed",
  "suggested_research_direction": "Proceed with integrated stratigraphic-petrophysical framework. Consider collaboration with oil company for data access."
}
```

### Example: Reservoir Engineering - Medium Feasibility

**Input Brief:**
```json
{
  "research_question": "Can machine learning methods improve water-cut prediction accuracy in heterogeneous sandstone reservoirs compared to traditional decline curve analysis?",
  "domain": "Oil & Gas Sciences",
  "sub_domain": "Reservoir Engineering",
  "key_concepts": ["machine learning", "water-cut prediction", "heterogeneous reservoir", "decline curve analysis", "production forecasting"],
  "research_type": "empirical"
}
```

**Output:**
```json
{
  "research_question": "Can machine learning methods improve water-cut prediction accuracy in heterogeneous sandstone reservoirs compared to traditional decline curve analysis?",
  "domain": "Oil & Gas Sciences",
  "feasibility_score": "medium",
  "related_work_summary": {
    "papers": [
      {"title": "Machine Learning for Oil Production Forecasting", "year": 2023, "relevance": "highly related", "key_insight": "Reviews ML methods but doesn't focus on water-cut"},
      {"title": "Water Cut Prediction Using Neural Networks", "year": 2021, "relevance": "highly related", "key_insight": "Applies NN but limited to homogeneous reservoirs"},
      {"title": "Data-Driven Production Forecasting in Oil Fields", "year": 2022, "relevance": "related", "key_insight": "General framework but not water-cut specific"}
    ],
    "datasets": [
      {"name": "Production data", "source": "Field operators", "relevance": "high"},
      {"name": "PVT data", "source": "Laboratory reports", "relevance": "medium"},
      {"name": "Rock physics", "source": "Core analysis", "relevance": "medium"}
    ],
    "ongoing_research": []
  },
  "novelty_assessment": {
    "innovation_points": [
      "Applies ML specifically to water-cut in heterogeneous reservoirs",
      "Compares with traditional decline curve methods rigorously"
    ],
    "differentiation_from_existing": "Extends prior ML work by addressing reservoir heterogeneity and comparing with traditional methods",
    "risk_of_collision": "medium"
  },
  "feasibility_factors": {
    "data_availability": "limited",
    "methodological_complexity": "medium",
    "resource_requirements": "medium",
    "ethical_considerations": "none"
  },
  "recommendation": "proceed",
  "suggested_research_direction": "Proceed but ensure data access. Consider using public datasets or collaborating with operators. May need to simplify to specific reservoir type."
}
```

### Example: Hydrogeology - High Feasibility

**Input Brief:**
```json
{
  "research_question": "What is the impact of long-term groundwater overdraft on hydrochemical evolution and quality of shallow aquifers in the North China Plain?",
  "domain": "Geology",
  "sub_domain": "Hydrogeology",
  "key_concepts": ["groundwater overdraft", "hydrochemical evolution", "aquifer salinization", "North China Plain", "groundwater quality"],
  "research_type": "empirical"
}
```

**Output:**
```json
{
  "research_question": "What is the impact of long-term groundwater overdraft on hydrochemical evolution and quality of shallow aquifers in the North China Plain?",
  "domain": "Geology",
  "feasibility_score": "high",
  "related_work_summary": {
    "papers": [
      {"title": "Groundwater Quality Evolution in North China Plain", "year": 2022, "relevance": "highly related", "key_insight": "Documents quality changes but limited temporal coverage"},
      {"title": "Hydrochemical Response to Groundwater Exploitation", "year": 2021, "relevance": "highly related", "key_insight": "Shows salinization patterns but not mechanism"},
      {"title": "Regional Groundwater Sustainability Assessment", "year": 2020, "relevance": "related", "key_insight": "Broader assessment without detailed chemistry"}
    ],
    "datasets": [
      {"name": "Groundwater quality monitoring data", "source": "Ministry of Water Resources", "relevance": "high"},
      {"name": "Pumping records", "source": "Local water bureaus", "relevance": "high"},
      {"name": "Precipitation data", "source": "CMA", "relevance": "medium"}
    ],
    "ongoing_research": []
  },
  "novelty_assessment": {
    "innovation_points": [
      "Long-term (20+ years) hydrochemical data analysis",
      "Explicit link between overdraft rates and water quality degradation"
    ],
    "differentiation_from_existing": "Extends prior work with longer time series and quantitative relationship between exploitation and quality",
    "risk_of_collision": "low"
  },
  "feasibility_factors": {
    "data_availability": "available",
    "methodological_complexity": "medium",
    "resource_requirements": "medium",
    "ethical_considerations": "none"
  },
  "recommendation": "proceed",
  "suggested_research_direction": "Proceed with focus on temporal trends and causal mechanisms"
}
```

### Example: Structural Geology - Medium Feasibility

**Input Brief:**
```json
{
  "research_question": "How did the Cenozoic tectonic evolution of the Qaidam Basin influence the formation and distribution of structural traps for hydrocarbon accumulation?",
  "domain": "Geology",
  "sub_domain": "Structural Geology",
  "key_concepts": ["Cenozoic tectonics", "structural trap", "basin evolution", "Qaidam Basin", "hydrocarbon accumulation"],
  "research_type": "theoretical"
}
```

**Output:**
```json
{
  "research_question": "How did the Cenozoic tectonic evolution of the Qaidam Basin influence the formation and distribution of structural traps for hydrocarbon accumulation?",
  "domain": "Geology",
  "feasibility_score": "medium",
  "related_work_summary": {
    "papers": [
      {"title": "Cenozoic Tectonic Evolution of Qaidam Basin", "year": 2021, "relevance": "highly related", "key_insight": "Documents tectonic history but not hydrocarbon implications"},
      {"title": "Structural Styles and Hydrocarbon Traps in Qaidam Basin", "year": 2020, "relevance": "highly related", "key_insight": "Describes traps but not their tectonic control"},
      {"title": "Basin-Mountain Coupling and Hydrocarbon Accumulation", "year": 2022, "relevance": "related", "key_insight": "Theoretical framework but not basin-specific"}
    ],
    "datasets": [
      {"name": "Seismic reflection data", "source": "CNPC/Qhai Oilfield", "relevance": "high"},
      {"name": "Balanced cross-sections", "source": "Published studies", "relevance": "medium"},
      {"name": "Fission track data", "source": "Lab analysis", "relevance": "medium"}
    ],
    "ongoing_research": []
  },
  "novelty_assessment": {
    "innovation_points": [
      "Links tectonic evolution timeline to trap formation stages",
      "Integrates structural analysis with hydrocarbon system modeling"
    ],
    "differentiation_from_existing": "Unlike prior work that studies tectonics or traps separately, this provides temporal-spatial linkage",
    "risk_of_collision": "low"
  },
  "feasibility_factors": {
    "data_availability": "limited",
    "methodological_complexity": "high",
    "resource_requirements": "high",
    "ethical_considerations": "none"
  },
  "recommendation": "proceed",
  "suggested_research_direction": "Proceed with focus on integrating existing datasets. May need collaboration for seismic data access."
}
```

---

## Quality Checklist

Before outputting feasibility report, verify:

- [ ] Searched at least 3 different sources
- [ ] Reviewed at least 10 related papers
- [ ] Identified clear differentiation points
- [ ] Assessed collision risk honestly
- [ ] Recommendation matches evidence

---

## Early Exit Conditions

This module can exit early if:

1. **Found identical work** → Immediately recommend pivot/abandon
2. **Found 5+ highly relevant papers** → Recommend pivot with differentiation needed
3. **Clear path to novelty** → Proceed with confidence

---

## Integration with Downstream Modules

The feasibility report is used by:

1. **Deep Researcher** — Uses `related_work_summary` to focus literature review
2. **Paper Drafting** — Uses `novelty_assessment` to frame contribution
3. **User** — Receives `recommendation` and `feasibility_score`

---

## Error Handling

| Error | Handling |
|-------|----------|
| No search results | Return "unknown" feasibility, suggest broader search |
| Search API failures | Try alternative sources, note in report |
| Invalid brief format | Request valid input from idea-parser |