---
name: research-plagiarism-detector
title: Research Plagiarism Detector
description: "Detect similarity between paper and existing literature across all academic domains."
version: 1.0.0
author: Sisyphus
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Research, Plagiarism Detection, Similarity Analysis, Academic Integrity, Domain-Agnostic, Geography, Remote Sensing, GIS, Geology, Petroleum, Hydrogeology]
    category: research
    related_skills: [research-consistency-checker, research-style-humanizer, research-paper-drafting]
    requires_toolsets: [llm, web_search, embedding_model]
---

# Research Plagiarism Detector

This module detects similarity between a paper and existing literature to ensure the paper doesn't have excessive overlap with published work and to identify areas that need rewriting.

---

## When To Use This Skill

Use this skill when:
- **Pre-submission check** — Ensure paper is original
- **Identify problematic sections** — Find highly similar passages
- **Verify novelty** — Confirm paper contributes new text
- **After drafting** — Check before finalizing

---

## Core Philosophy

1. **Detect, don't just score** — Identify specific problematic areas
2. **Context matters** — Similarity in methods section is different from introduction
3. **Threshold-based action** — Not all similarity is problematic
4. **False positive handling** — Common phrases shouldn't trigger alerts

---

## Input Format

```json
{
  "paper": {
    "title": "...",
    "sections": {
      "abstract": "...",
      "introduction": "...",
      "method": "...",
      "results": "...",
      "conclusion": "..."
    }
  },
  "reference_papers": [
    {"title": "...", "year": 2024, "doi": "...", "content": "..."}
  ],
  "threshold": {
    "high_similarity": 0.3,
    "medium_similarity": 0.15
  }
}
```

---

## Output Format

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

---

## Processing Steps

### Step 1: Collect Reference Papers

Get papers to compare against:

```python
def collect_references(research_topic: str, literature: LiteratureReview) -> List[Paper]:
    """Collect relevant papers for comparison."""
    
    # Use papers from literature review
    reference_papers = [p for p in literature.papers if p.relevance == "high"]
    
    # Add recent papers in the field
    recent_papers = search_recent_papers(research_topic, limit=10)
    
    return deduplicate(reference_papers + recent_papers)
```

### Step 2: Compute Textual Similarity

Use embedding-based similarity:

```python
def compute_textual_similarity(paper_text: str, reference_texts: List[str]) -> List[SimilarityResult]:
    """Compute embedding-based similarity between texts."""
    
    # Get embeddings
    paper_embedding = embedding_model.encode(paper_text)
    reference_embeddings = [embedding_model.encode(ref) for ref in reference_texts]
    
    # Compute cosine similarity
    similarities = []
    for ref_emb in reference_embeddings:
        sim = cosine_similarity(paper_embedding, ref_emb)
        similarities.append(sim)
    
    return similarities
```

### Step 3: Compute Semantic Similarity

Check for idea-level similarity:

```python
def check_semantic_similarity(paper_claims: List[str], reference_claims: List[str]) -> List[SemanticSimilarity]:
    """Check for semantic/idea-level similarity."""
    
    similarities = []
    
    for claim in paper_claims:
        # Find most similar reference claim
        best_match = None
        best_score = 0
        
        for ref_claim in reference_claims:
            score = semantic_similarity(claim, ref_claim)
            if score > best_score:
                best_score = score
                best_match = ref_claim
        
        similarities.append({
            "paper_claim": claim,
            "similar_claim": best_match,
            "score": best_score
        })
    
    return similarities
```

### Step 4: Identify Problematic Regions

Find specific areas with high similarity:

```python
def identify_problematic_regions(paper_sections: Dict[str, str], references: List[Paper]) -> List[Issue]:
    """Identify specific regions with similarity issues."""
    
    issues = []
    
    for section_name, section_text in paper_sections.items():
        # Split into paragraphs
        paragraphs = section_text.split("\n\n")
        
        for i, para in enumerate(paragraphs):
            # Check each paragraph against references
            for ref in references:
                similarity = compute_paragraph_similarity(para, ref.content)
                
                if similarity > HIGH_THRESHOLD:
                    issues.append({
                        "type": "textual_similarity",
                        "severity": "high",
                        "location": f"{section_name}.paragraph_{i}",
                        "matching_paper": ref.title,
                        "matched_text": find_matching_text(para, ref.content),
                        "similarity_score": similarity
                    })
                elif similarity > MEDIUM_THRESHOLD:
                    issues.append({
                        "type": "semantic_similarity",
                        "severity": "medium",
                        "location": f"{section_name}.paragraph_{i}",
                        "matching_paper": ref.title,
                        "suggested_rewrite": True
                    })
    
    return issues
```

### Step 5: Generate Rewrite Suggestions

Provide specific suggestions for problematic areas:

```python
def generate_rewrite_suggestions(issue: Issue, paper_text: str) -> str:
    """Generate specific rewrite suggestions for an issue."""
    
    prompt = f"""
    The following passage has high similarity to existing work:
    
    Problematic passage: {issue.matched_text}
    Original text: {issue.location}
    
    Generate a rewrite that:
    1. Preserves the technical accuracy
    2. Uses different phrasing and structure
    3. Adds original analysis or interpretation
    4. Maintains the same length or shorter
    
    Suggested rewrite:
    """
    
    return llm.generate(prompt)
```

---

## Similarity Thresholds

| Similarity Score | Severity | Action Required |
|-----------------|----------|-----------------|
| > 50% | Critical | Rewrite required |
| 30-50% | High | Significant rewrite needed |
| 15-30% | Medium | Paraphrase recommended |
| < 15% | Low | Acceptable |

---

## Section-Specific Considerations

Different sections have different acceptable similarity levels:

| Section | Acceptable Similarity | Notes |
|---------|----------------------|-------|
| Abstract | < 10% | Should be highly original |
| Introduction | < 20% | Some background is expected |
| Related Work | < 30% | By nature discusses others' work |
| Method | < 15% | Technical descriptions should be original |
| Results | < 10% | Should be your original analysis |
| Conclusion | < 15% | Should contain original insights |

---

## Example Output

### Example: Good Similarity Score

**Input:** A newly written paper on dynamic example selection

**Output:**
```json
{
  "overall_similarity": 0.07,
  "status": "pass",
  "sections": [
    {
      "section": "abstract",
      "similarity_score": 0.05,
      "issues": []
    },
    {
      "section": "introduction",
      "similarity_score": 0.11,
      "issues": [
        {
          "type": "semantic_similarity",
          "severity": "low",
          "location": "introduction.paragraph_2",
          "matching_paper": "Chain-of-Thought Prompting",
          "matched_text": "Recent work has shown that prompting strategies...",
          "suggested_rewrite": null
        }
      ]
    },
    {
      "section": "method",
      "similarity_score": 0.04,
      "issues": []
    },
    {
      "section": "results",
      "similarity_score": 0.03,
      "issues": []
    }
  ],
  "statistics": {
    "total_sections_checked": 6,
    "sections_with_issues": 1,
    "high_similarity_regions": 0,
    "medium_similarity_regions": 1
  },
  "recommendations": [
    "Paper is in good shape - minimal revision needed",
    "Consider adding more original interpretation in introduction discussion"
  ]
}
```

### Example: Needs Revision

**Input:** A paper with some copied content

**Output:**
```json
{
  "overall_similarity": 0.28,
  "status": "needs_revision",
  "sections": [
    {
      "section": "method",
      "similarity_score": 0.35,
      "issues": [
        {
          "type": "textual_similarity",
          "severity": "high",
          "location": "method.section_2",
          "matching_paper": "Active Prompting",
          "matched_text": "We select examples based on their embedding similarity to the query input...",
          "suggested_rewrite": "Our selection mechanism identifies examples whose vector representations most closely align with the current input, enabling dynamic adaptation to each query."
        }
      ]
    },
    {
      "section": "results",
      "similarity_score": 0.18,
      "issues": [
        {
          "type": "semantic_similarity",
          "severity": "medium",
          "location": "results.table_1",
          "matching_paper": "Self-Consistency",
          "matched_text": "Table shows performance across multiple reasoning tasks",
          "suggested_rewrite": "Consider adding more original analysis of why certain tasks benefit more"
        }
      ]
    }
  ],
  "statistics": {
    "total_sections_checked": 6,
    "sections_with_issues": 3,
    "high_similarity_regions": 1,
    "medium_similarity_regions": 4
  },
  "recommendations": [
    "CRITICAL: Rewrite method section 2 with original phrasing",
    "Add original analysis to results section",
    "Review all tables to ensure proper attribution"
  ]
}
```

---

## Quality Checklist

Before outputting plagiarism report, verify:

- [ ] Checked against relevant papers (at least 10)
- [ ] Applied section-specific thresholds
- [ ] Identified specific problematic regions
- [ ] Provided actionable rewrite suggestions
- [ ] Distinguishes textual vs semantic similarity

---

## Integration with Downstream Modules

The plagiarism report is used by:

1. **Paper Drafting** — Receives revision suggestions
2. **Style Humanizer** — Works on revised content
3. **User** — Receives final approval/rejection

---

## Technical Notes

### Embedding Model Selection

| Model | Use Case |
|-------|----------|
| bge-large | Best quality, slower |
| bge-base | Good quality, faster |
| sentence-transformers | Easy setup |

### Handling Common Phrases

Exclude common academic phrases from similarity checking:

```python
COMMON_PHRASES = [
    "extensive experiments show",
    "we conduct experiments on",
    "as shown in table",
    "in recent years",
    "a significant amount of research"
]
```

---

## Error Handling

| Error | Handling |
|-------|----------|
| No reference papers | Require literature review first |
| API failure | Use local embedding model |
| Ambiguous matches | Flag for human review |