---
name: interview
description: Allows defining a list of questions to ask the user. Questions have answers assigned to variables that can be referenced elsewhere. Supports validation criteria for acceptable answers.
---

# Interview Skill

Conduct structured interviews by parsing a question definition, presenting each question to the user via AskUserQuestion, validating responses against optional criteria, and storing answers in named variables for later reference.

## Input Format

The interview definition uses the following syntax:

```
* $VARIABLE_NAME = Question text here?
  * Validation or acceptance criteria for the answer

* $ANOTHER_VAR = Another question here?
```

### Variable Naming

- Variables begin with `$` followed by uppercase letters, digits, and underscores.
- Examples: `$THEME`, `$DB_ENGINE`, `$ENABLE_AUTH`, `$MAX_RETRIES`

### Validation Criteria

- Indented under the question, prefixed with `*`.
- Describes what constitutes an acceptable answer.
- When criteria specify exact acceptable values (e.g., "ONLY 'dark' or 'light'"), those values are used as AskUserQuestion options and the answer is enforced to match one of them.
- When criteria are absent or open-ended, reasonable defaults are generated and the user may freely type their own answer.

---

## Execution Workflow

### Step 1: Parse the Interview Definition

Extract from the provided input:

1. Each `* $VARIABLE = Question` pair, in order.
2. Any indented validation criteria beneath each question.
3. Any contextual notes.

If the input is empty or unparseable, use AskUserQuestion to ask the user to provide their interview questions and show them the expected format.

### Step 2: Ask Each Question

Process questions **in the order they are defined**. For each question:

#### 2a. Determine AskUserQuestion Options

Analyze the question text and its validation criteria to decide what options to present.

**Closed validation** — the criteria restrict answers to specific values (look for keywords like "ONLY", "must be one of", "acceptable values are", or an explicit enumeration of choices):

- Use the specified values as AskUserQuestion options.
- If the user selects "Other" and types a value that does not match an acceptable value, **re-ask** the question. Explain that only the listed values are accepted. The AskUserQuestion tool always shows an "Other" free-text field; closed validation is enforced by re-asking, not by hiding the field.

**Open validation or no validation** — the criteria are absent, advisory, or do not enumerate a fixed set:

- Infer 2-4 reasonable default options from the question's context.
- The user can pick a default or type anything via "Other".
- If validation criteria exist (e.g., "must be a positive integer"), validate after the user answers and re-ask if the answer does not satisfy the criteria.

#### 2b. Build the AskUserQuestion Call

- **question**: The question text from the definition.
- **header**: A short label (max 12 characters) derived from the variable name. Strip the `$` prefix, convert underscores to spaces, and title-case. Truncate if necessary. Examples: `$THEME` → `"Theme"`, `$DB_ENGINE` → `"DB Engine"`, `$ENABLE_AUTH` → `"Enable Auth"`.
- **options**: The options determined in step 2a. Each option needs a `label` (the value) and a `description` (a brief note on what choosing this means, if helpful; otherwise restate the value).
- **multiSelect**: `false` unless the question or criteria explicitly indicate multiple selections are allowed.

#### 2c. Validate the Response

After the user answers:

1. If **closed validation** applies and the answer is not one of the accepted values, inform the user why and re-ask. Do not proceed until a valid answer is given.
2. If **open validation** applies and the answer does not satisfy the criteria, inform the user why and re-ask.
3. If no validation applies, accept the answer as-is.

#### 2d. Store the Answer

Record the variable name and its validated answer for later reference.

### Step 3: Handle Conditional Questions

If a question's text references a previously assigned variable (e.g., "If $THEME is dark, would you like high-contrast mode?"):

- Evaluate the condition against stored variable values.
- If the condition is not met, **skip** the question and do not assign its variable.
- If the condition is met, ask the question normally.

### Step 4: Compile and Present Results

After all questions are answered, output a summary:

```markdown
## Interview Complete

| Variable | Value |
|----------|-------|
| $THEME | dark |
| $ACCESSIBILITY | yes |

### Variable Reference

These variables are now set for this session. Reference them by name in subsequent prompts:

- `$THEME` = "dark"
- `$ACCESSIBILITY` = "yes"
```

### Step 5: Variable Substitution in Subsequent Prompts

When the user provides a follow-up prompt that contains `$VARIABLE` references:

- Replace each `$VARIABLE` with its stored value.
- If a variable is referenced but was never assigned (or was skipped due to a failed condition), flag it and ask the user to provide a value.
- Conditional blocks like `if $VAR then ...` should be evaluated: execute the block only if `$VAR` is truthy (non-empty and not "no"/"false"/"0").

---

## Example

### Interview Definition

```
* $THEME = Would you prefer to use the light theme or the dark theme?
  * Answer is acceptable if the user replies ONLY with 'dark' or 'light'.

* $ACCESSIBILITY = Would you like to enable accessibility features?
  * Answer should be 'yes' or 'no'.

* $FONT_SIZE = What base font size would you like (in pixels)?
  * Must be a number between 12 and 24.
```

### Execution

**Question 1** — `$THEME`:
- Closed validation: only "dark" or "light".
- AskUserQuestion options: `[{label: "dark", description: "Dark color scheme"}, {label: "light", description: "Light color scheme"}]`
- If user types "blue" via Other → re-ask: "Only 'dark' or 'light' are accepted."

**Question 2** — `$ACCESSIBILITY`:
- Closed validation: "yes" or "no".
- AskUserQuestion options: `[{label: "yes", description: "Enable accessibility features"}, {label: "no", description: "Skip accessibility features"}]`

**Question 3** — `$FONT_SIZE`:
- Open validation: a number between 12 and 24.
- AskUserQuestion options: `[{label: "14", description: "Default — comfortable reading size"}, {label: "16", description: "Slightly larger"}, {label: "18", description: "Large text"}]`
- If user types "8" via Other → re-ask: "Font size must be between 12 and 24."

### Subsequent Prompt

User writes:
```
The user has decided to use the $THEME theme. Please set that as their default.
if $ACCESSIBILITY then Set the 'show_accessibility_tutorial' flag to true.
```

Substituted:
```
The user has decided to use the dark theme. Please set that as their default.
Set the 'show_accessibility_tutorial' flag to true.
```

(The `if $ACCESSIBILITY` block executes because `$ACCESSIBILITY` = "yes", which is truthy.)

---

## Guidelines

- Always ask questions in the order they are defined.
- Keep AskUserQuestion headers concise (max 12 characters).
- When generating default options, choose the most common or reasonable answers for the question's domain.
- Never skip validation. If criteria are provided, enforce them.
- Be conversational but efficient. Do not add unnecessary preamble before each question.
- If re-asking due to validation failure, clearly state what went wrong and what is expected.
- After the interview is complete, always show the full variable summary table.
