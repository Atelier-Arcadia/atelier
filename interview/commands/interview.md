---
description: Conduct a structured interview, assigning answers to variables for later reference
---

# Interview

The user has invoked `/interview:interview` to conduct a structured interview.

**Interview Definition**: $ARGUMENTS

## Instructions

1. Parse the interview definition from $ARGUMENTS using the format described in the interview skill.
2. If $ARGUMENTS is empty or missing, use AskUserQuestion to ask the user to provide their interview questions in the expected format. Show them an example.
3. Execute the interview skill workflow: parse questions, ask each one via AskUserQuestion, validate answers, and compile results.
