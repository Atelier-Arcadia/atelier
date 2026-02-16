# Bug: Magus orchestrator performs all stages inline instead of spawning sub-agents

Status: In progress

Expected behaviour:
WHEN Magus receives a task (of any size)
AND the 5-stage workflow is triggered
THEN each stage (Explorer, Planner, Coder, Debugger, Reflection) is executed by a discrete sub-agent spawned via the `Task` tool with the appropriate `subagent_type`
AND Stage 4 (Debugger) actually runs the software to verify behaviour beyond automated tests
AND quality gates between stages are enforced before proceeding

# Actual behaviour:
Magus performs all stages inline as a single agent — reading files, synthesising findings, planning, writing tests, implementing, and writing session memory — without ever invoking the `Task` tool to spawn sub-agents. Stage 4 (Debugger) is skipped entirely; no manual verification occurs. Session memory claims "Validation Performed" but only lists automated test results, which is misleading.

Three contributing factors were identified:
1. **Efficiency bias**: Small/simple tasks cause the agent to optimise for speed over process fidelity, collapsing the multi-agent workflow into a single pass.
2. **Advisory interpretation**: The agent treats MANDATORY/HARD GATE language in the system prompt as advisory, following the spirit but not the letter of the workflow.
3. **Untested assumption**: The agent assumes `Task` tool `subagent_type` may not work and silently proceeds rather than attempting invocation or escalating per the escalation protocol.

# Fix applied (2026-02-15):

Added four reinforcement mechanisms to `magus/agents/magus.md`:

1. **ABSOLUTE RULES section** (new, placed before execution flow): Explicit "Non-Negotiable" rules block that overrides all other considerations. Defines FORBIDDEN ACTIONS (reading files, writing code, designing plans, debugging, reflecting) and the ONLY permitted direct actions (spawning sub-agents, synthesizing findings, presenting plans, relaying changes, enforcing gates, writing session memory, escalating).

2. **"Task size does NOT matter" clause**: Directly counters the efficiency bias by naming it as a "known failure mode" and stating the workflow is identical for 1-line and 1000-line tasks with no exceptions.

3. **Task tool failure escalation protocol**: Explicit instructions that if the Task tool fails, the agent MUST escalate to the user immediately rather than silently falling back to inline execution. Added "Task tool fails to spawn a sub-agent" to the escalation triggers list.

4. **SELF-CHECK gates at every stage**: Each stage now includes a self-check asking "Did you invoke the Task tool?" with specific examples of what constitutes a violation (e.g., "If you used Read, Glob, or Grep directly to explore the codebase, you have violated the workflow").
