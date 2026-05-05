# Crossref API for Paper Search and Bibliometric Data Collection

When Semantic Scholar rate-limits or returns empty results, the Crossref REST API is a reliable alternative. It has very generous rate limits, returns structured JSON, and covers the majority of peer-reviewed journal articles indexed with DOIs.

## Quick Reference

| Action | Endpoint |
|--------|----------|
| Search by title/keyword | `GET https://api.crossref.org/works?query=...&rows=N` |
| Get paper by DOI | `GET https://api.crossref.org/works/DOI` |
| Filter to journal articles | Add `&filter=type:journal-article` |
| Sort by relevance | `&sort=relevance` (default) |

## Search by Topic (Python)

```python
import urllib.request, urllib.parse, json

def search_crossref(query, rows=100, offset=0):
    params = {
        'query': query,
        'rows': str(rows),
        'offset': str(offset),
        'filter': 'type:journal-article',
        'sort': 'relevance'
    }
    url = "https://api.crossref.org/works?" + urllib.parse.urlencode(params)
    req = urllib.request.Request(url, headers={'User-Agent': 'YourApp/1.0 (mailto:you@example.com)'})
    with urllib.request.urlopen(req, timeout=25) as resp:
        data = json.loads(resp.read())
    items = data.get('message', {}).get('items', [])
    total = data.get('message', {}).get('total-results', 0)
    return items, total
```

## Extracting Paper Metadata

Each item in the response contains:

| Field | Path | Notes |
|-------|------|-------|
| Title | `item['title'][0]` | Always a list, take first element |
| Year | `item['published-print']['date-parts'][0][0]` | Fallback: `item['created']['date-parts'][0][0]` |
| Journal | `item['container-title'][0]` | May be empty for conference papers |
| DOI | `item['DOI']` | Always present |
| Authors | `item['author']` | List of `{given, family}` dicts |
| Abstract | `item['abstract']` | Often present but not guaranteed |
| Subjects | `item['subject']` | List of subject strings |
| Cited-by count | `item['is-referenced-by-count']` | Crossref's own citation count (lower than S2's) |

## Complete Bibliometric Collection Pattern

```python
all_papers = []
seen_dois = set()

def process_items(items):
    added = 0
    for item in items:
        doi = item.get('DOI', '')
        if doi in seen_dois: continue
        seen_dois.add(doi)
        
        title = (item.get('title') or [''])[0]
        if not title: continue
        
        y = item.get('published-print', {}).get('date-parts', [[None]])[0][0]
        if not y:
            y = item.get('created', {}).get('date-parts', [[None]])[0][0]
        if not y or not (2000 <= y <= 2025): continue  # Filter by year range
        
        all_papers.append({
            'title': title,
            'year': y,
            'authors': [f"{a.get('family','')}, {a.get('given','')}" for a in item.get('author', [])[:10]],
            'journal': (item.get('container-title') or [''])[0],
            'doi': doi,
            'abstract': (item.get('abstract', '') or '')[:500],
            'subjects': item.get('subject', []),
            'citedBy': item.get('is-referenced-by-count', 0)
        })
        added += 1
    return added

# Collect from multiple queries
for query in ['"urban food security"', '"urban food system" climate', '"urban agriculture" food']:
    time.sleep(1.0)  # Polite delay
    items, total = search_crossref(query, 100)
    n = process_items(items)
    print(f"Query '{query}': +{n} papers (total available: {total})")
```

## Key Differences from Semantic Scholar

| Feature | Crossref | Semantic Scholar |
|---------|----------|-----------------|
| Rate limit | Very generous (~50 req/min) | Strict (1 req/sec, frequent 429s) |
| Citation counts | `is-referenced-by-count` (lower) | `citationCount` (higher, more comprehensive) |
| Abstracts | Often included | Almost always included |
| Year range | Good coverage 2000+ | Good coverage 1950+ |
| Chinese queries | Limited (mostly Western journals) | Limited (same) |
| Response format | JSON with nested `message.items` | JSON with flat `data` array |

## Fallback Strategy

1. Try Semantic Scholar first (better citation data, abstracts)
2. If S2 returns HTTP 429, wait 30 seconds and retry once
3. If S2 returns empty results for a multi-word query, try `curl` with `%20` in terminal
4. If S2 still fails, switch to Crossref API — it's slower per-request but has effectively unlimited quota
5. For bibliometric analysis: Crossref provides enough metadata (title, authors, year, journal, DOI, subjects) for trend analysis, journal ranking, and thematic mapping
6. Missing from Crossref: Citation networks require S2 or manual Scopus/WoS export