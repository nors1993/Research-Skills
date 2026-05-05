# Paper Writing Workflow Pitfalls

## Critical Rule: Do Not Skip Steps 1-2

The most common error when writing research papers is skipping the Feasibility Study
(Step 1) and Deep Research (Step 2) to jump directly to drafting (Step 3).

This leads to:
- **Fabricated literature**: Citations without real DOIs; papers that don't exist
- **Fabricated data**: Bibliometric numbers, survey results, or experimental data invented to fill gaps
- **Weak differentiation**: Paper fails to distinguish itself from existing work because the landscape wasn't surveyed first

## Step 2 Hard Constraint

The workflow requires ≥15 real, verifiable papers with DOIs before drafting can begin.
If fewer than 15 papers are found, the task MUST exit with an explanation to the user.
AI-hallucinated references are strictly forbidden.

## Domain-Specific Search Strategy

| Domain | Primary Source | Fallback |
|--------|---------------|----------|
| CS, Math, Physics | arXiv API | Semantic Scholar |
| Social Sciences | **Crossref API** | Semantic Scholar, Google Scholar |
| Medical | PubMed, Crossref | Semantic Scholar |
| Engineering | IEEE Xplore, Crossref | Semantic Scholar |

Crossref API query pattern:
```
curl "https://api.crossref.org/works?query=TERMS&rows=15&filter=type:journal-article"
```
Parse with: `python3 -c "import sys,json; ..."` (returns JSON, no rate limits on free tier).
