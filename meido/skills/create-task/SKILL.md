# Skill: create-task

Creates feature requests or bug reports from user prompts.

## Instructions

When the user describes a task, determine whether it is a **feature request** or a **bug report** based on the following criteria:

- **Feature request**: The user is requesting something NEW to be built.
- **Bug report**: The user is describing an issue or defect in current functionality.

Both have a "Status" that is updated when we work on features and bugs.

### Feature Requests

1. Attempt to format the request using the template below.
2. If any information is missing or unclear, use AskUserQuestion to ask the user for clarification before proceeding.
3. Once complete, create a file at `docs/features/<YYYY-MM-DD>-<feature-name>.md` (use today's date and a kebab-case feature name).

Feature request format:

```
# Feature: [feature name]

Status: Not started

[desired behaviour]

Done Criteria:
* [ ] checklist item
```

The "Done Criteria" section must contain a concrete checklist of acceptance criteria derived from the user's description. Each item should be a verifiable condition.

### Bug Reports

1. Attempt to format the report using the template below.
2. If any information is missing or unclear, use AskUserQuestion to ask the user for clarification before proceeding. At minimum you need: the condition/trigger, the expected outcome, and the actual behaviour.
3. Once complete, create a file at `docs/bugs/<YYYY-MM-DD>-<bug-name>.md` (use today's date and a kebab-case bug name).

Bug report format:

```
# Bug: [bug title]

Status: Not started

Expected behaviour:
WHEN [condition is met]
AND [follow-up action takes place (optional, any number)]
THEN [outcome observed]

# Actual behaviour:
[Description of what actually happens]
```

### Guidelines

- Infer as much as possible from the user's prompt before asking for clarification.
- Keep titles and file names concise and descriptive.
- Create the `docs/features/` or `docs/bugs/` directory if it does not already exist.
- After writing the file, confirm the file path and show a summary of what was documented.
