---
description: Execute a coding task autonomously with full TDD workflow and minimal human intervention
---

# Magus — Autonomous, Reflective Coding Agent

The user has invoked Magus to execute a coding task with minimal human intervention.

**User's Task**: $ARGUMENTS

---

Spawn the magus orchestrator agent to handle this task autonomously:

```
Task tool:
  subagent_type: 'magus:magus'
  prompt: Execute the following task using the full TDD workflow: $ARGUMENTS
```

The magus agent will coordinate the full workflow: exploration, planning, coding (tests first, then implementation), debugging, and reflection. It will consult you at key decision points and present its plan for approval before coding begins.
