# Crossref API Quick Reference

## Why Crossref
Crossref is the most reliable free source for social science, medical, and humanities literature.
Unlike arXiv (CS/math/physics) and Semantic Scholar (often rate-limited at 429), Crossref:
- Returns real, DOI-verified papers
- Has no rate limits on the free tier
- Covers all academic domains
- Returns clean JSON

## Basic Search
```bash
curl -s "https://api.crossref.org/works?query=TERMS+HERE&rows=10&filter=type:journal-article" \
  | python3 -c "
import sys, json
data = json.load(sys.stdin)
items = data.get('message', {}).get('items', [])
for i, item in enumerate(items):
    title = item.get('title', ['No title'])[0]
    year = item.get('published-print', {}).get('date-parts', [[0]])[0][0]
    authors = ', '.join([a.get('family', '?') for a in item.get('author', [])[:3]])
    doi = item.get('DOI', '')
    journal = item.get('container-title', [''])[0]
    print(f'{i+1}. [{year}] {title[:100]}')
    print(f'   Authors: {authors} | {journal[:50]}')
    print(f'   DOI: {doi}')
    print()
"
```

## Filters
- `filter=type:journal-article` — exclude books, proceedings
- `filter=from-pub-date:2015` — date range start
- `filter=until-pub-date:2025` — date range end

## When to Use
- Social science literature searches (food security, urban studies, policy, economics)
- When Semantic Scholar returns 429 "Too Many Requests"
- When arXiv returns irrelevant results (common for non-CS topics)
