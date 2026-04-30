---
name: Markdown style conventions
description: >-
  Blank lines around headings and lists; fenced
  code blocks must carry a language tag
type: feedback
---

# Markdown style conventions

- Blank line **before and after** every heading
- Blank line **before and after** every list
- Fenced code blocks must carry a language tag
  (` ```sh `, ` ```yaml `, ` ```text `, etc.)

**Why:** ensures consistent rendering across
GitHub, IDE previews, and Markdown linters; some
renderers misformat lists or code blocks without
proper spacing.

**How to apply:** apply to README.md, docs, and
any Markdown content embedded in comments. When
editing existing Markdown, fix violations you
notice in the surrounding lines.
