# Documentation

This directory contains project documentation, architectural decisions, and analysis.

## Structure

- **[decisions/](decisions/)** - Architecture Decision Records (ADRs) documenting significant architectural and design choices
- **[discoveries/](discoveries/)** - Non-obvious problems, root causes, solutions, and prevention strategies
- **[plans/](plans/)** - Implementation plans and design documents
- **Analysis documents** - Research and pattern extraction from external projects

## Knowledge Management Patterns

The `decisions/` and `discoveries/` directories implement opt-in knowledge management patterns:

- **Use when**: Project-specific knowledge that benefits the team, belongs in git history, or requires formal documentation
- **Personal alternative**: Use `mem` for solo work and personal knowledge tracking
- **Setup**: Run `/setup-knowledge-management` to initialize these patterns in a new project

See the README in each directory for templates and detailed guidance.
