---
name: research-style-humanizer
title: Research Style Humanizer
description: "Remove AI writing patterns and create natural academic prose for any discipline."
version: 1.0.0
author: Sisyphus
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Research, Writing Style, AI Detection, Academic Writing, Domain-Agnostic, Geography, Remote Sensing, GIS, Geology, Petroleum, Hydrogeology]
    category: research
    related_skills: [research-plagiarism-detector, research-consistency-checker, research-paper-drafting]
    requires_toolsets: [llm, files]
---

# Research Style Humanizer

This module removes AI writing patterns from research papers to create more natural, human-like academic prose while maintaining technical accuracy.

---

## When To Use This Skill

Use this skill when:
- **Remove AI patterns** — Make text less "AI-sounding"
- **Improve naturalness** — Create more authentic academic voice
- **Post-drafting polish** — Final pass before submission
- **After plagiarism check** — Ensure original voice

---

## Core Philosophy

1. **Preserve accuracy** — Never compromise technical correctness
2. **Reduce patterns, not content** — Remove style, keep substance
3. **Iterative improvement** — Multiple passes for best results
4. **Verify after change** — Ensure no degradation

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
      "experiments": "...",
      "results": "...",
      "conclusion": "..."
    }
  },
  "target_style": "formal_academic | conversational_academic | standard",
  "aggression_level": "light | medium | aggressive"
}
```

---

## Output Format

```json
{
  "status": "success | needs_iteration",
  "humanized_sections": {
    "abstract": {
      "original_word_count": 150,
      "humanized_word_count": 145,
      "changes_made": [
        "Removed excessive transition words",
        "Added more direct phrasing",
        "Reduced pattern repetition"
      ]
    }
  },
  "statistics": {
    "total_sections": 6,
    "sections_modified": 6,
    "word_count_change": -5%,
    "pattern_reduction": {
      "transition_words": -40%,
      "sentence_length_variance": +15%,
      "pattern_markers": -60%
    }
  },
  "ai_detection_score": {
    "before": 0.85,
    "after": 0.35,
    "improvement": "-50%"
  },
  "human_score": {
    "before": 0.2,
    "after": 0.75,
    "improvement": "+55%"
  }
}
```

---

## AI Writing Pattern Detection

### Step 1: Identify AI Patterns

```python
def detect_ai_patterns(text: str) -> List[AIPattern]:
    """Detect AI writing patterns in text."""
    
    patterns = []
    
    # Pattern 1: Overuse of transition words
    transition_words = ["furthermore", "moreover", "additionally", "consequently", "subsequently"]
    transition_count = sum(1 for w in transition_words if w in text.lower())
    
    if transition_count > 2:
        patterns.append(AIPattern(
            type="excessive_transitions",
            severity="high" if transition_count > 4 else "medium",
            count=transition_count,
            locations=find_locations(text, transition_words)
        ))
    
    # Pattern 2: Overly formal/hedging language
    hedging = ["it is important to note", "it should be noted", "it is worth mentioning"]
    for phrase in hedging:
        if phrase in text.lower():
            patterns.append(AIPattern(
                type="hedging_phrases",
                severity="medium",
                count=1,
                locations=[text.lower().find(phrase)]
            ))
    
    # Pattern 3: Pattern repetition in sentence structure
    if detect_sentence_patterns(text):
        patterns.append(AIPattern(
            type="sentence_structure_patterns",
            severity="medium",
            count=detect_sentence_patterns(text)
        ))
    
    # Pattern 4: Excessive qualification
    qualifiers = ["significantly", "remarkably", "notably", "substantially"]
    if sum(1 for q in qualifiers if q in text.lower()) > 3:
        patterns.append(AIPattern(
            type="excessive_qualifiers",
            severity="medium"
        ))
    
    # Pattern 5: Generic opening sentences
    generic_openers = ["in recent years", "it is well known", "recent studies have shown"]
    for opener in generic_openers:
        if text.lower().startswith(opener):
            patterns.append(AIPattern(
                type="generic_openers",
                severity="low"
            ))
    
    return patterns
```

### Step 2: Humanize Text

```python
def humanize_text(text: str, patterns: List[AIPattern], aggression: str) -> str:
    """Apply humanization to text based on detected patterns."""
    
    # Use LLM to rewrite with humanization instructions
    prompt = f"""
    Rewrite the following academic text to sound more natural and human-written
    while maintaining technical accuracy.
    
    Target style: {"light" if aggression == "light" else "moderate" if aggression == "medium" else "aggressive"} humanization
    
    AI patterns to remove:
    {format_patterns(patterns)}
    
    Original text:
    {text}
    
    Requirements:
    1. Preserve all technical content and accuracy
    2. Remove formulaic AI patterns
    3. Use more direct, varied phrasing
    4. Maintain academic tone
    5. Don't change any facts, results, or technical details
    
    Humanized text:
    """
    
    return llm.generate(prompt)
```

---

## Pattern Categories & Fixes

### 1. Transition Word Overload

**Problem:**
```
Furthermore, we observe that...
Moreover, our method...
Additionally, experiments show...
```

**Fix:**
```
We observe that...
Our method...
Experiments show...
```

### 2. Hedging Phrases

**Problem:**
```
It is important to note that...
It should be noted that...
It is worth mentioning that...
```

**Fix:**
```
We note that...
We observe that...
Note that...
```

### 3. Sentence Structure Repetition

**Problem:**
```
We first introduce X. We then describe Y. We finally present Z.
Our method consists of A. Our approach utilizes B. Our framework employs C.
```

**Fix:**
```
We introduce X, then describe Y, and finally present Z.
Our method combines A, B, and C in a unified framework.
```

### 4. Excessive Qualification

**Problem:**
```
significantly improves
remarkably effective
notably outperforms
substantially better
```

**Fix:**
```
improves
effective
outperforms
better
```

### 5. Generic Openers

**Problem:**
```
In recent years, large language models have...
It is well known that deep learning...
Recent studies have shown that...
```

**Fix:**
```
Large language models have...
Deep learning enables...
Contemporary research shows...
```

### 6. Chinese Academic AI Patterns (中文论文特有)

These are the most frequent AI-tell markers in Chinese academic prose. **Rule: delete the phrase and start the sentence directly — the content is almost always stronger without the wrapper.**

| Pattern | Frequency | Fix |
|---------|-----------|-----|
| `需要指出的是，` | Very high | Drop it; start directly |
| `值得关注的是，` | Very high | Drop it; start directly |
| `值得注意的是，` | Very high | Drop it; start directly |
| `应当指出的是，` | High | Drop it or use a specific subject |
| `众所周知，` | Medium | Replace with a specific citation or drop |
| `不难发现，` | Medium | Drop; state the finding directly |
| `总而言之，` | Medium | Drop or use a one-sentence summary signal |

**Before:** `值得注意的是，Starlink单一星座的在轨卫星数已超过6,000颗...`  
**After:** `Starlink单一星座的在轨卫星数已超过6,000颗...`

The pattern is always the same: these phrases add zero information and sound like an AI stalling for time. Search for them with `search_files pattern='需要指出的|值得关注的|值得注意的是|应当指出|众所周知|不难发现|总而言之'` after drafting.

---

## Multi-Pass Humanization

```
┌─────────────────────────────────────────────────────────────┐
│              HUMANIZATION PIPELINE                          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Input: AI-written text                                     │
│      │                                                      │
│      ▼                                                      │
│  ┌───────────────────────────────────────────────────────┐  │
│  │ Pass 1: Pattern Detection                              │  │
│  │ - Identify all AI patterns                             │  │
│  │ - Classify by severity                                 │  │
│  │ - Map locations                                        │  │
│  └───────────────────────────────────────────────────────┘  │
│      │                                                      │
│      ▼                                                      │
│  ┌───────────────────────────────────────────────────────┐  │
│  │ Pass 2: Light Humanization                             │  │
│  │ - Remove obvious patterns                              │  │
│  │ - Fix overused transitions                             │  │
│  │ - Add directness                                       │  │
│  └───────────────────────────────────────────────────────┘  │
│      │                                                      │
│      ▼                                                      │
│  ┌───────────────────────────────────────────────────────┐  │
│  │ Pass 3: Structure Variation                            │  │
│  │ - Vary sentence lengths                                │  │
│  │ - Mix sentence types                                   │  │
│  │ - Reduce formulaic structures                          │  │
│  └───────────────────────────────────────────────────────┘  │
│      │                                                      │
│      ▼                                                      │
│  ┌───────────────────────────────────────────────────────┐  │
│  │ Pass 4: Verification                                   │  │
│  │ - Check technical accuracy maintained                  │  │
│  │ - Verify no facts changed                              │  │
│  │ - Run AI detection score                               │  │
│  └───────────────────────────────────────────────────────┘  │
│      │                                                      │
│      ▼                                                      │
│  Output: Humanized text                                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Verification Steps

After humanization, verify:

```python
def verify_humanization(original: str, humanized: str) -> VerificationResult:
    """Verify humanization didn't break anything."""
    
    # Check 1: Technical accuracy
    technical_preserved = verify_technical_content(original, humanized)
    
    # Check 2: No hallucinated content
    no_hallucinations = verify_no_new_claims(original, humanized)
    
    # Check 3: AI detection score improved
    detection_score = check_ai_detection(humanized)
    
    # Check 4: Readability maintained
    readability = compute_readability(humanized)
    
    return VerificationResult(
        technical_accuracy_preserved=technical_preserved,
        no_new_hallucinations=no_hallucinations,
        ai_detection_improved=detection_score < 0.4,
        readability_acceptable=readability > 0.5
    )
```

---

## Example Output

### Example: Before/After

**Original (AI-written):**
```
Furthermore, it is important to note that our method significantly
outperforms existing approaches. Moreover, we observe that the
improvement is notably consistent across different datasets. Additionally,
our approach demonstrates remarkably effective performance in terms
of both accuracy and efficiency. Consequently, we believe that these
findings substantially contribute to the field.
```

**Humanized:**
```
Our method outperforms existing approaches, and we observe consistent
improvement across different datasets. The approach achieves strong
performance in both accuracy and efficiency, contributing to the field.
```

### Statistics:
```json
{
  "before": {
    "word_count": 85,
    "transition_words": 4,
    "ai_detection_score": 0.82
  },
  "after": {
    "word_count": 52,
    "transition_words": 0,
    "ai_detection_score": 0.28
  },
  "changes": {
    "transition_words_removed": 4,
    "word_count_reduced": "-39%",
    "ai_score_improved": "-54%"
  }
}
```

---

## Quality Checklist

Before outputting humanized paper, verify:

- [ ] All technical content preserved
- [ ] No facts or claims changed
- [ ] AI detection score improved (target < 0.4)
- [ ] Readability maintained
- [ ] No new content added

---

## Integration with Downstream Modules

The humanized paper is the final output:

1. **User** — Receives final, submission-ready paper
2. **Optional: Human Review** — Can request additional polish
3. **Archive** — Store as final version

---

## Technical Notes

### AI Detection Tools

| Tool | Purpose |
|------|---------|
| GPTZero | Detect AI-generated content |
| Originality.ai | Check for plagiarism + AI |
| Turnitin | Academic submission check |

### Readability Metrics

| Metric | Target Range |
|--------|-------------|
| Flesch Reading Ease | 30-50 (academic) |
| Gunning Fog | 17-20 (academic) |
| Sentence Length Variance | > 15% (more natural) |

---

## Error Handling

| Error | Handling |
|-------|----------|
| Over-humanization | Reduce aggression level |
| Accuracy lost | Revert to original, use lighter pass |
| Style too casual | Use "formal_academic" target |