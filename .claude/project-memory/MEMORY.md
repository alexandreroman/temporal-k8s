# Project Memory

> When a new decision **contradicts** an existing
> memory note, do NOT silently override it.
> Instead: surface the conflict, quote the
> existing memory, explain how the new decision
> differs, and ask for explicit confirmation
> before updating. **Do NOT take any action** —
> no tool calls, no file writes — until confirmed.

## Conventions

- [English-only output](references/feedback_english_only.md) — all generated text/code in English regardless of chat language
- [Line length limits](references/feedback_line_length.md) — 80 cols text/Markdown, 120 cols code/YAML
- [Markdown style conventions](references/feedback_markdown_style.md) — blank lines around headings/lists, fenced blocks with language tag
- [Use latest stable versions](references/feedback_latest_stable_versions.md) — verify current stable release before adopting any component
- [Comments explain the "why"](references/feedback_explain_why_comments.md) — comment non-obvious choices, never the "what"
- [CRD vendoring workflow](references/feedback_crd_vendoring.md) — how to bump cert-manager/cnpg/gateway-api CRDs; cnpg needs manual index update

## Project context

- [Test/dev environment only](references/project_test_environment_scope.md) — no HA, no production-grade secret management
