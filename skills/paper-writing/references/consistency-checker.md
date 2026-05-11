---
name: research-consistency-checker
title: Research Consistency Checker
description: "Validate paper logical consistency and claim-evidence alignment for any academic domain."
version: 1.0.0
author: Sisyphus
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Research, Validation, Logic Checking, Review, Domain-Agnostic, Geography, Remote Sensing, GIS, Geology, Petroleum, Hydrogeology]
    category: research
    related_skills: [research-paper-drafting, research-plagiarism-detector, research-style-humanizer]
    requires_toolsets: [llm, files]
---

# Research Consistency Checker

This module validates that a research paper has logical consistency, all claims are properly supported by evidence, and there are no contradictions throughout the document.

---

## When To Use This Skill

Use this skill when:
- **Validate paper logic** — Check for internal contradictions
- **Verify claim-evidence mapping** — Ensure every claim has supporting evidence
- **Cross-reference consistency** — Check consistency across sections
- **Pre-submission review** — Final check before submitting

---

## Core Philosophy

1. **Every claim needs evidence** — No unsupported assertions
2. **Logical flow must be intact** — No contradictions between sections
3. **Traceability** — Claims should be traceable to experiments
4. **Multi-perspective review** — Use multiple agents for thorough checking

---

## Input Format

```json
{
  "paper": {
    "title": "...",
    "abstract": "...",
    "introduction": "...",
    "method": "...",
    "experiments": "...",
    "results": "...",
    "conclusion": "..."
  },
  "experiment_log": {
    "experiment_1": {"claim": "...", "evidence": "...", "result": "..."}
  },
  "check_types": ["logic", "evidence", "consistency", "traceability"]
}
```

---

## Output Format

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

---

## Processing Steps

### Step 1: Extract All Claims

First, identify all claims made in the paper:

```python
def extract_claims(paper: Paper) -> List[Claim]:
    """Extract all claims from the paper."""
    
    prompt = f"""
    Extract all explicit and implicit claims from this paper.
    
    A claim is any statement that the paper asserts as true, including:
    - Direct assertions ("Our method achieves X")
    - Comparative statements ("Our method is better than X")
    - Causal claims ("X causes Y")
    - Quantitative claims ("We observe X% improvement")
    
    Paper Sections:
    - Introduction: {paper.introduction[:2000]}
    - Method: {paper.method[:2000]}
    - Results: {paper.results[:2000]}
    
    Return a list of claims with their locations.
    """
    
    return llm.generate(prompt, schema=List[Claim])
```

### Step 2: Map Claims to Evidence

For each claim, find supporting evidence:

```python
def map_claims_to_evidence(claims: List[Claim], experiment_log: ExperimentLog) -> ClaimEvidenceMap:
    """Map each claim to its supporting evidence."""
    
    mapping = {}
    
    for claim in claims:
        # Find relevant experiment
        evidence = find_evidence(claim, experiment_log)
        
        mapping[claim.id] = {
            "claim_text": claim.text,
            "evidence": evidence,
            "location": claim.location,
            "supported": evidence is not None,
            "confidence": "high" if evidence else "low"
        }
    
    return mapping
```

### Step 3: Check for Logical Contradictions

Use multiple perspectives to find contradictions:

```python
def check_contradictions(paper: Paper, claims: List[Claim]) -> List[Contradiction]:
    """Check for logical contradictions in the paper."""
    
    # Use different "reviewer" perspectives
    perspectives = [
        "methodological_reviewer",  # Focus on methodology consistency
        "results_reviewer",          # Focus on result consistency
        "theory_reviewer"           # Focus on theoretical claims
    ]
    
    all_issues = []
    
    for perspective in perspectives:
        issues = check_with_perspective(paper, claims, perspective)
        all_issues.extend(issues)
    
    # Deduplicate similar issues
    return deduplicate_issues(all_issues)
```

### Step 4: Verify Terminology Consistency

Check for inconsistent use of terms:

```python
def check_terminology_consistency(paper: Paper) -> List[TerminologyIssue]:
    """Check for inconsistent terminology across sections."""
    
    prompt = f"""
    Check for terminology inconsistencies in this paper.
    
    Look for:
    1. Same concept referred to by different terms
    2. Same term used with different meanings
    3. Inconsistent capitalization/hyphenation
    
    Paper:
    {format_sections(paper)}
    
    Report any inconsistencies found.
    """
    
    return llm.generate(prompt, schema=List[TerminologyIssue])
```

### Step 5: Verify Traceability

Ensure each result can be traced back to experiments:

```python
def verify_traceability(paper: Paper, experiment_log: ExperimentLog) -> TraceabilityReport:
    """Verify that all reported results can be traced to experiments."""
    
    prompt = f"""
    Verify traceability of all results in this paper.
    
    For each result/claim in the paper:
    1. Identify which experiment supports it
    2. Verify the numbers match
    3. Check if the interpretation is accurate
    
    Paper Results: {paper.results}
    Experiment Log: {format_experiment_log(experiment_log)}
    
    Report any traceability gaps.
    """
    
    return llm.generate(prompt, schema=TraceabilityReport)
```

---

## Multi-Agent Validation Pattern

Use the autoreason pattern for thorough checking:

```
┌─────────────────────────────────────────────────────┐
│         CONSISTENCY VALIDATION LOOP                 │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Input: Paper Draft                                  │
│      │                                              │
│      ▼                                              │
│  ┌───────────────────────────────────────────────┐  │
│  │ Agent 1: Claim Extractor                      │  │
│  │ - Extract all claims from each section       │  │
│  │ - Tag claim type (method/result/comparison)  │  │
│  └───────────────────────────────────────────────┘  │
│      │                                              │
│      ▼                                              │
│  ┌───────────────────────────────────────────────┐  │
│  │ Agent 2: Evidence Mapper                       │  │
│  │ - For each claim, find supporting evidence   │  │
│  │ - Map to specific experiments/results         │  │
│  └───────────────────────────────────────────────┘  │
│      │                                              │
│      ▼                                              │
│  ┌───────────────────────────────────────────────┐  │
│  │ Agent 3: Contradiction Detector               │  │
│  │ - Check for logical contradictions           │  │
│  │ - Cross-reference claims between sections    │  │
│  │ - Flag any inconsistencies                    │  │
│  └───────────────────────────────────────────────┘  │
│      │                                              │
│      ▼                                              │
│  ┌───────────────────────────────────────────────┐  │
│  │ Agent 4: Terminology Verifier                 │  │
│  │ - Check term consistency                      │  │
│  │ - Verify definitions align                    │  │
│  └───────────────────────────────────────────────┘  │
│      │                                              │
│      ▼                                              │
│  Synthesize: Combine all findings                   │
│      │                                              │
│      ▼                                              │
│  Output: Validation Report                           │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

## Issue Classification

### Critical Issues

| Issue Type | Example | Action |
|------------|---------|--------|
| Unsupported major claim | "Our method achieves SOTA" without results | Require evidence or remove claim |
| Contradiction | Method says X, Results show not X | Resolve contradiction |
| Result fabrication | Numbers don't match experiment log | Verify or correct numbers |

### Major Issues

| Issue Type | Example | Action |
|------------|---------|--------|
| Missing evidence | Claim has weak evidence | Add more supporting data |
| Terminology inconsistency | Same concept called different things | Standardize terminology |
| Traceability gap | Result not linked to experiment | Add experiment reference |

### Minor Issues

| Issue Type | Example | Action |
|------------|---------|--------|
| Vague language | "significantly improved" without numbers | Add specific numbers |
| Formatting inconsistency | Tables formatted differently | Standardize |

---

## Example Output

### Example Validation Report

**Input:** Paper claiming "DES improves reasoning by 15%"

**Output:**
```json
{
  "validation_results": {
    "overall_status": "needs_revision",
    "issues": [
      {
        "type": "claim_evidence_mismatch",
        "severity": "major",
        "location": "abstract.paragraph_2",
        "description": "Claim '15% improvement' appears in abstract but only 12% is reported in results",
        "suggested_fix": "Update abstract to state '12% improvement' or verify correct number"
      },
      {
        "type": "terminology_inconsistency",
        "severity": "minor",
        "location": "introduction.methodology",
        "description": "Term 'similarity-based selection' used in intro, 'embedding-based selection' used in method",
        "suggested_fix": "Choose one term and use consistently"
      },
      {
        "type": "traceability_gap",
        "severity": "major",
        "location": "results.section_3",
        "description": "Claim about 'consistent improvements across all tasks' has no supporting table reference",
        "suggested_fix": "Add reference to specific table showing per-task results"
      }
    ],
    "claim_evidence_map": {
      "claim_1": {"supported": true, "evidence": "exp_1", "location": "results.table_1"},
      "claim_2": {"supported": false, "evidence": null, "location": "abstract.para_2"}
    },
    "statistics": {
      "total_claims": 18,
      "supported_claims": 16,
      "unsupported_claims": 2,
      "contradictions": 0,
      "terminology_inconsistencies": 3
    }
  },
  "revision_needed": true,
  "revision_instructions": "1. Fix abstract claim to match results (15% → 12%). 2. Standardize terminology. 3. Add table references to claims about task-level results."
}
```

---

## Quality Checklist

Before outputting validation report, verify:

- [ ] All major claims have evidence
- [ ] No logical contradictions found
- [ ] Terminology is consistent throughout
- [ ] All results can be traced to experiments
- [ ] Numbers in claims match actual results

---

## Integration with Downstream Modules

The validation report is used by:

1. **Paper Drafting** — Receives revision instructions
2. **Plagiarism Detector** — Works on validated paper version
3. **Style Humanizer** — Works on validated paper version

---

## Error Handling

| Error | Handling |
|-------|----------|
| Cannot extract claims | Request clearer paper text |
| No experiment log | Request experiment data from upstream |
| Too many issues | Prioritize critical/major, defer minor |