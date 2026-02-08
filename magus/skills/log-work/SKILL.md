# Skill: log-work

Logs a summary of the current session's work to a timestamped markdown file.

## Instructions

1. Run `date '+%Y-%m-%d_%H-%M'` to get the current date and time.
2. Ask the user for a short name describing the session's focus (e.g. "auth-refactor", "fix-scroll-bug"). Use this as the `<name>` component.
3. Review the conversation history to identify:
   - What was accomplished during this session
   - Any problems or blockers encountered
   - Outstanding tasks or follow-ups for the next session
4. Create the `docs/work-log/` directory if it does not already exist.
5. Create a file at `docs/work-log/<date-time>-<name>.md` using the date-time from step 1 and the name from step 2 (e.g. `docs/work-log/2026-02-03_14-30-auth-refactor.md`).

Use the following template:

```
# Work Log: <name>

**Date:** <date and time>

## Accomplished

- <bullet point summary of each thing achieved>

## Problems Encountered

- <bullet point for each issue, or "None" if there were no problems>

## Follow-up Tasks

- [ ] <task to pick up in the next session, or "None" if there are no follow-ups>
```

### Guidelines

- Be concise but specific in each bullet point.
- For the "Accomplished" section, focus on outcomes (what changed) rather than process (what commands were run).
- For "Problems Encountered", include enough context that someone returning to the project can understand the issue.
- For "Follow-up Tasks", use a checkbox list so items can be tracked.
- After writing the file, confirm the file path to the user.
