# Feature: Magus agent refactor

Status: Not started

Refactor the magus plugin so that the TDD development workflow currently encoded in the `core` skill is instead driven by a top-level `magus` agent. Additionally, merge the `test-writer` and `implementer` agents (stages 3a and 3b, currently spawned in parallel) into a single `coder` agent that handles both test authoring and implementation sequentially.

### Rationale

1. **Skill vs Agent entrypoint** - The `core` skill (`magus/skills/core/SKILL.md`) currently acts as the master orchestrator for the entire TDD workflow, spawning sub-agents for each stage. This orchestration logic is more naturally expressed as an agent definition rather than a skill, since it represents a persistent, multi-stage workflow rather than a discrete user-invokable action.

2. **Parallel test-writer + implementer UX issues** - Stages 3a (`test-writer`) and 3b (`implementer`) currently run as separate agents in parallel. Because of how Claude Code's task system surfaces parallel agent work, this creates a confusing user experience. Merging them into a single `coder` agent that writes tests first (RED) then writes implementation (GREEN) in sequence provides a clearer, more predictable UX while preserving the TDD discipline.

Done Criteria:
* [ ] A new `magus` agent definition exists (e.g. `magus/agents/magus.md`) that encodes the full TDD orchestration workflow currently in `magus/skills/core/SKILL.md`
* [ ] The `core` skill is removed or replaced with a thin wrapper that delegates to the `magus` agent
* [ ] The `test-writer` and `implementer` agents are replaced by a single `coder` agent (e.g. `magus/agents/coder.md`)
* [ ] The `coder` agent performs test authoring (RED phase) and implementation (GREEN phase) sequentially within a single agent context
* [ ] The `magus` agent orchestration references the new `coder` agent instead of parallel `test-writer`/`implementer` spawns
* [ ] All other stages (explorer, planner, debugger, reflection) continue to function as before
* [ ] Session memory writing remains mandatory and functional
* [ ] Hard gates and escalation triggers are preserved in the new `magus` agent
