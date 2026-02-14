# Plan: Magus Agent Refactor

## Context

The magus plugin's TDD workflow is currently encoded in a skill (`magus/skills/core/SKILL.md`), but this orchestration logic is better expressed as an agent definition since it represents a multi-stage workflow that spawns sub-agents. Additionally, the test-writer and implementer agents run in parallel during stage 3, which creates a confusing UX due to how Claude Code surfaces parallel tasks. This refactor moves the orchestration into a `magus` agent and merges the two parallel coding agents into a single sequential `coder` agent.

## Changes

### 1. Create `magus/agents/coder.md` — the merged test-writer + implementer

Combines the RED and GREEN phases into a single agent that works sequentially:

- **Phase 1**: Understand the plan and study existing patterns (from both agents' "Understanding" phases)
- **Phase 2**: Write failing tests (RED) — taken from `test-writer.md`
- **Phase 3**: Write implementation to make tests pass (GREEN) — taken from `implementer.md`
- **Phase 4**: Verify all tests pass, check for regressions

Frontmatter:
```yaml
name: coder
description: Writes tests first (RED), then implements code to make them pass (GREEN). Full TDD cycle in a single agent.
model: sonnet
tools: [Read, Write, Bash, Glob, Grep]
```

Key design decisions:
- Preserves the TDD discipline (tests FIRST, then implementation)
- Keeps the verification steps from both agents
- Output format combines both agents' formats (test files created + implementation files created + test results)
- Maintains the "present full file contents" requirement for the orchestrator to relay

### 2. Create `magus/agents/magus.md` — the orchestrator agent

Moves the orchestration workflow from `skills/core/SKILL.md` into an agent definition. Modeled after how `oracle/agents/lead-orchestrator.md` works — an agent that coordinates sub-agents via the `Task` tool.

Frontmatter:
```yaml
name: magus
description: Master orchestrator for autonomous TDD development. Coordinates explorer, planner, coder, debugger, and reflection agents.
model: opus
tools: [Read, Write, Glob, Grep, Task]
```

The workflow stages become:
| Stage | Agent | Change from current |
|-------|-------|-------------------|
| 1 | explorer | No change |
| 2 | planner | No change |
| 3 | **coder** | Replaces parallel test-writer + implementer |
| 4 | debugger | No change |
| 5 | reflection | No change |

Preserves all existing content from the core skill:
- Prerequisites check
- Hard gates between stages
- Findings synthesis requirement (stage 1.1)
- User approval gate (stage 2)
- Escalation triggers
- Session memory (mandatory)
- Completion reporting
- Tone section

Key difference: Stage 3 is now a single `coder` agent invocation instead of parallel `test-writer` + `implementer`. References `Task` tool with `subagent_type` instead of `SpawnSubAgentTool`.

### 3. Update `magus/skills/core/SKILL.md` — thin wrapper

Replace the full orchestration logic with a thin skill that delegates to the `magus` agent:

```markdown
---
description: Execute a coding task autonomously with full TDD workflow and minimal human intervention
---

# Magus - Autonomous, Reflective Coding Agent

The user has invoked Magus to execute a coding task.

**User's Task**: $ARGUMENTS

Spawn the `magus` agent with the Task tool to handle this task:

Task tool:
  subagent_type: magus
  prompt: [user's task with full context]
```

### 4. Delete `magus/agents/test-writer.md` and `magus/agents/implementer.md`

These are fully replaced by `coder.md`.

## Files

| Action | File |
|--------|------|
| Create | `magus/agents/coder.md` |
| Create | `magus/agents/magus.md` |
| Rewrite | `magus/skills/core/SKILL.md` |
| Delete | `magus/agents/test-writer.md` |
| Delete | `magus/agents/implementer.md` |

## Verification

- Read through each new/modified file to verify completeness
- Confirm the `magus` agent preserves all hard gates, escalation triggers, and session memory requirements from the original core skill
- Confirm the `coder` agent covers both RED and GREEN phases with proper TDD discipline
- Confirm the thin skill wrapper correctly delegates to the agent
