# Agent Design Best Practices

Learnings from building the Hegelian Dialectical Research Oracle—a multi-agent system for comprehensive research through structured intellectual opposition.

---

## Core Principle: Agents Take Shortcuts

The fundamental insight from building this system: **agents will take the path of least resistance unless explicitly prevented**.

When we told domain researchers to "gather comprehensive information," they:
- Relied on search snippets instead of reading sources
- Produced summaries without textual evidence
- Skipped the WebFetch tool entirely

The solution was not better instructions—it was **explicit behavioral mandates** with verification.

---

## Agent Design Principles

### 1. Explicit > Implicit

Vague instructions produce vague results. Compare:

**Weak**: "Research this topic thoroughly"

**Strong**:
```
You MUST:
1. Use WebFetch to read at least 4 sources in full
2. Extract 2+ direct quotes from each source
3. Complete a Source Reading Log proving what you read
4. Document insights that required actual reading

DO NOT:
- Rely on search snippets
- Summarize sources you haven't fetched
- Skip WebFetch and go to synthesis
```

### 2. Behavioral Mandates Need DO/DO NOT Lists

Agents benefit from explicit prohibition of shortcuts:

```markdown
## CRITICAL: You Must Actually Read Sources

**DO NOT**:
- Rely solely on search result snippets
- Summarize sources you haven't fetched and read
- Make claims without direct textual evidence
- Skip WebFetch and go straight to synthesis
- Produce output without a populated Source Reading Log

**DO**:
- Use WebFetch to retrieve full content
- Quote directly from source text (not search snippets)
- Track what you learned that REQUIRED actual reading
```

### 3. Output Formats Shape Behavior

The structure of required output directly influences agent behavior.

**Problem**: When we asked for "1000-2000 tokens" of findings, agents skimmed.

**Solution**: Require specific sections that prove depth:

```markdown
### Source Reading Log
| # | Source | URL | Read Status | Key Quote | Novel Insight |
|---|--------|-----|-------------|-----------|---------------|
| 1 | [Name] | [URL] | FULL | "..." | What I learned beyond snippet |
```

The "Novel Insight" column forces agents to demonstrate learning beyond surface-level scanning.

### 4. Accountability Through Documentation

Require agents to document their process, not just their conclusions:

- **Source Reading Log**: Proves which sources were actually read
- **Phase summaries**: Shows work at each stage
- **Quality self-assessment**: Forces reflection on output quality

### 5. Pre-Submission Checklists

End agent prompts with verification checklists:

```markdown
## Final Checklist Before Submitting

- [ ] Source Reading Log has AT LEAST 4 sources marked FULL
- [ ] Each source has AT LEAST 2 direct quotes
- [ ] Novel Insights column shows learning beyond snippets
- [ ] Key Findings section includes inline citations
- [ ] Multiple perspectives are represented with evidence

**If any checkbox is unchecked, go back and read more sources.**
```

---

## Multi-Agent Orchestration Patterns

### The Orchestrator-Worker Pattern

Central coordinator deploys specialized workers:

```
                    ORCHESTRATOR
                   (Planning + Synthesis)
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
    WORKER A          WORKER B          WORKER C
   (Specialized)     (Specialized)     (Specialized)
```

**Orchestrator responsibilities**:
- Query analysis and planning
- Task delegation with clear boundaries
- **Quality verification** before proceeding
- Final synthesis

**Key insight**: The orchestrator must VERIFY worker output quality, not just collect results.

### Parallel vs Sequential Execution

| Stage | Execution | Rationale |
|-------|-----------|-----------|
| Information gathering | PARALLEL | Independent tasks, maximize speed |
| Position formation | PARALLEL (if multiple) | Can develop simultaneously |
| Opposition/critique | SEQUENTIAL | Requires full input from prior stage |
| Synthesis | SEQUENTIAL | Requires complete history |
| Verification | SEQUENTIAL | Quality gate before next phase |

### Verification Checkpoints

Insert quality gates between phases:

```
Phase 1: Domain Research (Parallel)
         │
         ▼
    ┌─────────────────┐
    │ VERIFICATION    │ ← Check Source Reading Logs
    │ CHECKPOINT      │   Check quote counts
    └────────┬────────┘   Check novel insights
             │
    [PASS]   │   [FAIL] → Request revision
             ▼
Phase 2: Thesis Formation
```

**Verification checklist example**:
```markdown
## Research Depth Verification

| Domain | Sources Claimed | Sources Verified Read | Quotes Found | Status |
|--------|-----------------|----------------------|--------------|--------|
| Economic | 6 | 6 (all FULL) | 14 | PASS |
| Cultural | 5 | 4 (1 PARTIAL) | 9 | PASS |

**Overall Verification**: PASSED - proceeding to thesis
```

### Information Passing Protocol

Each agent receives only what it needs:

```yaml
Domain Research Agent:
  receives:
    - Domain description
    - Research question
    - Depth profile requirements

Thesis Agent:
  receives:
    - Research question
    - Assigned perspective
    - Domain findings (condensed)

Antithesis Agent:
  receives:
    - Research question
    - COMPLETE thesis output  # Must see full thesis
    - Domain findings

Synthesis Agent:
  receives:
    - Research question
    - Thesis output
    - Antithesis output
    - Debate summary (if any)
    - Domain findings  # For factual grounding
```

**Key insight**: Preserve full dialectical chain for synthesis—don't over-summarize.

---

## Preventing Common Anti-Patterns

### Anti-Pattern: Premature Consensus

**Problem**: Agents naturally seek balance and consensus, weakening intellectual opposition.

**Solution**:
1. Separate API calls for thesis and antithesis
2. Explicit anti-hedging instructions:
   ```
   DO NOT include phrases like:
   - "however"
   - "on the other hand"
   - "it could be argued"
   You are COMMITTED to this position.
   ```

### Anti-Pattern: Surface-Level Research

**Problem**: Agents skim search snippets instead of reading sources.

**Solution**:
1. Three-phase structure: Discovery → Deep Reading → Synthesis
2. Require Source Reading Log with quotes
3. Verification checkpoint before proceeding
4. Quality flags in citation processing: `[SNIPPET ONLY]`, `[NOT READ]`

### Anti-Pattern: Generic Outputs

**Problem**: Agents produce vague, non-specific findings.

**Solution**:
1. Require direct quotes with inline citations
2. Demand specific evidence, not general summaries
3. "Novel Insight" requirements prove genuine engagement

### Anti-Pattern: Orchestrator Doing Worker Tasks

**Problem**: Orchestrator does primary research instead of delegating.

**Solution**: Explicit role boundary:
```
Your primary value is orchestration and final synthesis, NOT primary research.
- You coordinate; you do not do primary research yourself
- Deploy agents using the Task tool
- Wait for all subagent results before synthesizing
```

---

## Effort Scaling and Depth Profiles

### Configurable Depth Profiles

Not all tasks need the same depth. Define profiles:

| Profile | Sources | Quotes/Source | Verification |
|---------|---------|---------------|--------------|
| QUICK | 2 | 1 | Optional |
| STANDARD | 4 | 2 | Enabled |
| THOROUGH | 6 | 3 | Strict |
| EXHAUSTIVE | 10+ | 4+ | Multi-pass |

### Automatic Depth Selection

Map task complexity to depth:

| Complexity | Profile | Rationale |
|------------|---------|-----------|
| SIMPLE | QUICK | Factual lookup, no conflict |
| MODERATE | STANDARD | Some perspective variance |
| COMPLEX | THOROUGH | Expert disagreement |
| HIGHLY_CONTESTED | EXHAUSTIVE | Normative/value-laden |

### User Override

Allow explicit depth specification:
```
/command --quick "Simple question"
/command --exhaustive "Contested topic"
```

---

## Quality Assurance Mechanisms

### Quality Flags

Define flags that downstream agents can apply:

```markdown
### Research Depth Flags
- `[NOT READ]` - Source not in Reading Log
- `[SNIPPET ONLY]` - No evidence of full reading
- `[QUOTE NEEDED]` - Lacks direct textual support
- `[SHALLOW SOURCE]` - Source marked PARTIAL

### Citation Quality Flags
- `[SINGLE SOURCE]` - Only one source
- `[CONTESTED]` - Sources disagree
- `[NEEDS VERIFICATION]` - Inadequate sourcing
- `[OUTDATED]` - Source too old
```

### Multi-Layer Verification

1. **Self-verification**: Agent checklist before submission
2. **Orchestrator verification**: Quality gate between phases
3. **Citation processor verification**: Final quality check

### Feedback Loops

When verification fails, provide specific remediation:

```
Your previous research was too shallow. Please:
1. Use WebFetch to read MORE sources in full
2. Extract direct quotes from each source you read
3. Update your Source Reading Log with complete entries
4. Ensure you have at least [X] sources fully read
5. Document what you learned FROM READING
```

---

## Prompt Engineering Patterns

### The Three-Section Pattern

Structure agent prompts with:

1. **Role and Context**: Who they are, what they're doing
2. **Behavioral Requirements**: What they MUST do, what they must NOT do
3. **Output Format**: Exact structure of expected output

### Explicit Tool Guidance

Don't assume agents know which tools to use:

```markdown
## Research Process

### PHASE 2: Deep Reading (60% of effort)

For EACH source in your Source Queue:
1. **Use WebFetch** to retrieve the full content
2. **Read the entire article/page**
3. **Extract direct quotes** (at least 2 per source)
```

### Model Selection Guidance

Match model capability to task demands:

| Task Type | Recommended Model | Rationale |
|-----------|-------------------|-----------|
| Orchestration | opus | Complex planning |
| Research | sonnet | Fast parallel execution |
| Argumentation | sonnet | Strong reasoning |
| Synthesis | opus | Most demanding cognitive task |
| Citation processing | haiku | Structured extraction |

---

## Lessons Learned

### What Worked

1. **Source Reading Logs** - Created accountability for actually reading sources
2. **Three-phase structure** - Discovery → Deep Work → Synthesis provides clear progression
3. **Verification checkpoints** - Quality gates prevent shallow work from propagating
4. **Explicit anti-hedging** - Separate calls + prohibited phrases enable genuine opposition
5. **Quality flags** - Enable downstream verification and feedback

### What We Had to Fix

1. **Original**: "Research thoroughly" → agents skimmed snippets
   **Fixed**: Explicit WebFetch requirements + Source Reading Log

2. **Original**: 1000-2000 token output → encouraged brevity over depth
   **Fixed**: 2000-4000 tokens + structured sections proving engagement

3. **Original**: No verification step → shallow research proceeded to synthesis
   **Fixed**: Phase 2.5 verification checkpoint

4. **Original**: Thesis/antithesis in one call → weak opposition
   **Fixed**: Separate API calls with anti-hedging instructions

### Design Heuristics

1. **If you can't verify it, you can't trust it** - Build verification into the process
2. **Agents optimize for completion, not quality** - Explicit quality requirements needed
3. **Output format is behavior specification** - Design outputs that prove quality
4. **Shortcuts are the default** - Anti-patterns need explicit prohibition
5. **Separate calls for separate roles** - One prompt, one role, one perspective

---

## Template: New Agent Prompt

```markdown
---
name: [agent-name]
description: [One-line description of agent's purpose]
model: [sonnet/opus/haiku]
tools:
  - [List required tools]
---

# [Agent Name]

You are a [Role Description] for [System Name].

## Your Task

[Clear statement of what this agent does]

---

## CRITICAL Requirements

**YOU MUST**:
- [Explicit behavioral requirement 1]
- [Explicit behavioral requirement 2]
- [Explicit behavioral requirement 3]

**DO NOT**:
- [Explicit prohibition 1]
- [Explicit prohibition 2]
- [Explicit prohibition 3]

---

## Process

### Phase 1: [Name] ([X]% of effort)
[What to do in this phase]

### Phase 2: [Name] ([Y]% of effort)
[What to do in this phase]

### Phase 3: [Name] ([Z]% of effort)
[What to do in this phase]

---

## Output Format

```markdown
## [Section 1]
[What goes here]

## [Section 2]
[What goes here]

## [Verification Section]
[How agent proves quality of work]
```

---

## Final Checklist

- [ ] [Verification item 1]
- [ ] [Verification item 2]
- [ ] [Verification item 3]

**If any checkbox is unchecked, [remediation action].**
```

---

## Conclusion

Effective agent design requires:

1. **Explicit behavioral mandates** over vague instructions
2. **Verification checkpoints** between phases
3. **Accountability mechanisms** like Source Reading Logs
4. **Quality flags** for downstream verification
5. **Anti-pattern prevention** through explicit prohibition
6. **Configurable depth** for appropriate scaling

The goal is not to constrain agents, but to channel their capabilities toward genuine quality rather than superficial completion.
