# Research Depth Configuration

This document defines configurable depth profiles for the Research Oracle's domain researchers.

---

## Depth Profiles

### QUICK (Speed Priority)

Use for simple factual lookups where speed matters more than comprehensiveness.

| Parameter | Value |
|-----------|-------|
| Minimum sources to read | 2 |
| Direct quotes required | 1 per source |
| Source Reading Log | Simplified |
| Verification checkpoint | Skipped |

**Use cases**:
- Simple factual questions ("What is X?")
- Time-sensitive queries
- Quick lookups with well-established answers

**Invoke with**: `--quick` or `-q`

---

### STANDARD (Balanced)

The baseline for typical research queries requiring some depth.

| Parameter | Value |
|-----------|-------|
| Minimum sources to read | 4 |
| Direct quotes required | 2 per source |
| Source Reading Log | Required |
| Verification checkpoint | Enabled |

**Use cases**:
- Most research queries
- Comparative analysis
- Moderately complex topics

**Invoke with**: Default for MODERATE complexity queries

---

### THOROUGH (Depth Priority) - DEFAULT

The default profile for substantive research. Prioritizes depth over speed.

| Parameter | Value |
|-----------|-------|
| Minimum sources to read | 6 |
| Direct quotes required | 3 per source |
| Source Reading Log | Detailed with quality ratings |
| Verification checkpoint | Strict enforcement |
| Cross-reference required | Yes (sources should cite each other or common authorities) |

**Use cases**:
- Complex or contested topics
- Questions with multiple valid perspectives
- Research requiring dialectical analysis

**Invoke with**: `--thorough` or `-t` (also default for COMPLEX queries)

---

### EXHAUSTIVE (Maximum Depth)

Academic-level rigor. For critical decisions or highly contested topics.

| Parameter | Value |
|-----------|-------|
| Minimum sources to read | 10+ |
| Direct quotes required | 4+ per source |
| Source Reading Log | Comprehensive with methodology notes |
| Verification checkpoint | Multi-pass verification |
| Cross-reference required | Mandatory with explicit tracking |
| Multiple reading passes | Encouraged (revisit sources with new context) |
| Contradictory source requirement | Must find and read sources from opposing viewpoints |

**Use cases**:
- HIGHLY_CONTESTED topics
- Academic-level research
- High-stakes decisions requiring maximum rigor
- Questions where being wrong has significant consequences

**Invoke with**: `--exhaustive` or `-e`

---

## Profile Selection Logic

The orchestrator should select depth profile based on:

### Automatic Selection (based on complexity classification)

| Query Complexity | Default Profile |
|------------------|-----------------|
| SIMPLE | QUICK |
| MODERATE | STANDARD |
| COMPLEX | THOROUGH |
| HIGHLY_CONTESTED | EXHAUSTIVE |

### User Override

Users can override automatic selection via command flags:

```
/oracle:consult --quick "What is the capital of France?"
/oracle:consult --thorough "What caused the 2008 financial crisis?"
/oracle:consult --exhaustive "Should AI development be regulated?"
```

### Escalation Rules

The orchestrator MAY escalate depth profile if:
- Initial research reveals the topic is more contested than expected
- Source Reading Log shows significant contradictions
- Thesis/antithesis positions are far apart on factual matters

---

## Source Reading Requirements by Profile

### Minimum Reading Time Investment

| Profile | Expected Reading Phase | Acceptable Discovery:Reading Ratio |
|---------|------------------------|-----------------------------------|
| QUICK | 10-15 mins | 50:50 |
| STANDARD | 20-30 mins | 30:70 |
| THOROUGH | 45-60 mins | 20:80 |
| EXHAUSTIVE | 90+ mins | 15:85 |

Note: These are expectations for the domain researcher agent, not wall-clock time.

### Source Diversity Requirements

| Profile | Perspective Diversity | Source Type Diversity |
|---------|----------------------|----------------------|
| QUICK | Not required | Any credible sources |
| STANDARD | Encouraged | At least 2 source types |
| THOROUGH | Required (opposing views) | At least 3 source types |
| EXHAUSTIVE | Mandatory (must seek contrary evidence) | All applicable source types |

---

## Quality Thresholds

### Verification Pass Criteria

| Profile | Min Sources Read | Min Quotes | Reading Log Complete | Novel Insights |
|---------|-----------------|------------|---------------------|----------------|
| QUICK | 2 | 2 total | Simplified OK | Optional |
| STANDARD | 4 | 8 total | Required | Expected |
| THOROUGH | 6 | 18 total | Detailed required | Required |
| EXHAUSTIVE | 10 | 40+ total | Comprehensive | Required with analysis |

### Verification Failure Actions

| Profile | On Failure |
|---------|------------|
| QUICK | Warn and proceed |
| STANDARD | Request one revision |
| THOROUGH | Request revision, block thesis formation |
| EXHAUSTIVE | Request multiple revisions if needed, strict blocking |

---

## Implementation Notes

### For Domain Researchers

When receiving a task with a depth profile:

1. Check the profile requirements at the start
2. Plan your Source Queue to exceed minimums
3. Track reading progress as you go
4. Self-verify before submitting output

### For Orchestrators

When deploying researchers:

1. Include depth profile in task prompt
2. Specify minimum requirements explicitly
3. Verify output against profile requirements
4. Escalate if verification fails

### Profile Flag Syntax

```
--quick, -q     : QUICK profile
--standard, -s  : STANDARD profile (rarely needed, usually auto-selected)
--thorough, -t  : THOROUGH profile
--exhaustive, -e: EXHAUSTIVE profile
```
