# Atelier

A Claude Code plugin marketplace.

## Plugins

### Oracle

Comprehensive research through Hegelian dialectical methodology. Oracle deploys specialized agents — domain researchers, thesis advocates, antithesis critics, debate moderators, and synthesis reconcilers — to investigate complex questions through structured intellectual opposition and resolution.

Rather than converging on a single perspective, Oracle forces genuine debate between committed positions before synthesizing a higher-order understanding that preserves what each side got right.

**Key features:**
- Deep source reading with verification (not just search snippets)
- Configurable depth profiles: quick, standard, thorough, exhaustive
- Citation processing with quality flags and reading verification
- Automatic complexity classification and depth scaling

### Magus

Autonomous test-driven development. Magus executes a full TDD pipeline — explore, plan, write tests, implement, debug, reflect — through specialized sub-agents that enforce quality at every stage.

Tests are written before implementation. Code is written to pass the tests. Nothing more, nothing less.

**Key features:**
- Six-stage TDD workflow with hard gates between phases
- Codebase exploration and pattern discovery before any changes
- Automatic debugging with root cause analysis
- Session reflection that extracts patterns and builds reusable skills

### Meido

Lightweight task and session management. Meido creates structured feature requests, bug reports, and timestamped work logs to keep projects organized.

**Key features:**
- Feature request and bug report generation from natural language
- Timestamped session work logs
- Structured markdown output with done criteria and follow-up tasks

### Interview

Structured user interviews with variable assignments. Interview parses a question definition format, presents each question via `AskUserQuestion` with reasonable default options, validates responses against optional criteria, and stores answers in named variables for use in subsequent prompts.

**Key features:**
- `$VARIABLE = Question` syntax for defining interview questions
- Closed validation enforces exact acceptable answers; open validation allows free-text input
- Conditional questions that evaluate based on previously assigned variables
- Variable substitution in follow-up prompts, including `if $VAR then ...` blocks
